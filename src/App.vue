<script setup>
import { ref } from 'vue'
import LessonCard from './components/LessonCard.vue'
import SiteHeader from './components/SiteHeader.vue'
import SortSearchBar from './components/SortSearchBar.vue'
import CartDrawar from './components/CartDrawar.vue'

const lessons = ref([])
const filteredLessons = ref([])
const cart = ref([])
const sortField = ref('')
const sortOrder = ref('')
const searchQuery = ref('')
const showDrawar = ref(false)

const fetchLessons = async () => {
  const response = await fetch('https://express-app-xyl5.onrender.com/lessons')
  const data = await response.json()
  lessons.value = data
}

fetchLessons()

const changeSorting = (field, order) => {
  if (!field || !order) return

  sortField.value = field
  sortOrder.value = order

  lessons.value.sort((a, b) => {
    if (field === 'Spaces' || field === 'Price') {
      return order === 'asc'
        ? Number(a[field]) - Number(b[field])
        : Number(b[field]) - Number(a[field])
    } else {
      return order === 'asc'
        ? a[field].localeCompare(b[field])
        : b[field].localeCompare(a[field])
    }
  })

  lessons.value = [...lessons.value]

  filteredLessons.value.sort((a, b) => {
    if (field === 'Spaces' || field === 'Price') {
      return order === 'asc'
        ? Number(a[field]) - Number(b[field])
        : Number(b[field]) - Number(a[field])
    } else {
      return order === 'asc'
        ? a[field].localeCompare(b[field])
        : b[field].localeCompare(a[field])
    }
  })

  filteredLessons.value = [...filteredLessons.value]
}

const changeSearchQuery = async query => {
  if (!query) {
    filteredLessons.value = []
    searchQuery.value = ''
    return
  }

  const response = await fetch(
    `https://express-app-xyl5.onrender.com/search?q=${query}`,
  )
  const data = await response.json()

  for (let lesson of cart.value) {
    const lessonIndex = data.findIndex(item => item.id === lesson.id)
    if (lessonIndex !== -1) {
      data[lessonIndex].Spaces -= lesson.quantity
    }
  }

  filteredLessons.value = data

  searchQuery.value = query

  changeSorting(sortField.value, sortOrder.value)
}

const addToCart = lessonId => {
  const lesson = lessons.value.find(lesson => lesson.id === lessonId)
  const existingCartItem = cart.value.find(item => item.id === lessonId)

  if (existingCartItem) {
    existingCartItem.quantity++
  } else {
    cart.value.push({
      id: lesson.id,
      sport: lesson.Sport,
      location: lesson.Location,
      price: lesson.Price,
      image: lesson.icon,
      quantity: 1,
    })
  }

  lesson.Spaces--

  const filteredLesson = filteredLessons.value.find(
    lesson => lesson.id === lessonId,
  )
  if (filteredLesson) filteredLesson.Spaces--

  changeSorting(sortField.value, sortOrder.value)

  showSnackbar('Item added to cart', true)
}

const toggleDrawar = () => {
  showDrawar.value = !showDrawar.value
}

const removeCartItem = id => {
  const lesson = lessons.value.find(lesson => lesson.id === id)
  const cartItem = cart.value.find(item => item.id === id)

  lesson.Spaces += cartItem.quantity

  const filteredLesson = filteredLessons.value.find(
    lesson => lesson.id === id,
  )
  if (filteredLesson) filteredLesson.Spaces += cartItem.quantity

  cart.value = cart.value.filter(item => item.id !== id)

  changeSorting(sortField.value, sortOrder.value)

  if (cart.value.length === 0) {
    toggleDrawar()
  }
}

const checkout = async (name, phone) => {
  const response = await fetch('https://express-app-xyl5.onrender.com/order', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      name: name,
      phone: phone,
      items: cart.value.map(item => ({
        id: item.id,
        quantity: item.quantity,
      })),
    }),
  })

  if (!response.ok) {
    showSnackbar('There was an error placing your order. Please try again later', false)
    return
  }

  showSnackbar('Order placed updating lesson availability...', true)

  for (let item of cart.value) {
    const response2 = await fetch('https://express-app-xyl5.onrender.com/lesson', {
      method: 'PUT',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        id: item.id,
        Spaces: item.quantity,
      }),
    })
    if (!response2.ok) {
      showSnackbar(`There was an error updating the lesson ${item.id} availability. Please try again later`, false)
    }
  }

  showSnackbar('Your order has been placed successfully', true)

  cart.value = []
  toggleDrawar()
}

const showSnackbar = (message, success) => {
  const snackbar = document.createElement('div')
  snackbar.className = 'snackbar'
  snackbar.textContent = message
  document.body.appendChild(snackbar)

  if(success) {
    snackbar.classList.add('success')
  }
  else {
    snackbar.classList.add('error')
  }

  setTimeout(() => {
    snackbar.classList.add('show')
  }, 100)

  setTimeout(() => {
    snackbar.classList.remove('show')
    setTimeout(() => {
      document.body.removeChild(snackbar)
    }, 300)
  }, 3000)
}
</script>

<template>
  <SiteHeader
    siteName="My Awesome Store"
    :cartDisabled="cart.length === 0"
    @toggleDrawar="toggleDrawar"
  />
  <main>
    <SortSearchBar
      :selectedSortField="sortField"
      :selectedSortOrder="sortOrder"
      :changeSort="changeSorting"
      :changeSearchQuery="changeSearchQuery"
    />

    <LessonCard
      v-for="lesson in searchQuery ? filteredLessons : lessons"
      :key="lesson.id"
      :lesson="lesson"
      :addToCart="addToCart"
    />
  </main>

  <CartDrawar
    :isDrawerVisible="showDrawar"
    :cartItems="cart"
    :removeCartItem="removeCartItem"
    :checkout="checkout"
  />
</template>

<style scoped></style>