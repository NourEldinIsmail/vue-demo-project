<script setup>
import { ref } from 'vue'
import LessonCard from './components/LessonCard.vue'
import SiteHeader from './components/SiteHeader.vue'
import SortSearchBar from './components/SortSearchBar.vue'
import CartPage from './components/CartPage.vue'
import CheckoutModal from './components/CheckoutModal.vue'

const lessons = ref([])
const filteredLessons = ref([])
const cart = ref([])
const sortField = ref('')
const sortOrder = ref('')
const searchQuery = ref('')
const showCart = ref(false)
const showModal = ref(false)
const modalTitle = ref('')
const modalMessage = ref('')

const fetchLessons = async () => {
  const response = await fetch('https://express-app-xyl5.onrender.com/lessons')
  const data = await response.json()
  lessons.value = data
}

fetchLessons()

const toggleCart = () => {
  showCart.value = !showCart.value
}

const changeSorting = (field, order) => {
  if(!field || !order) return

  sortField.value = field
  sortOrder.value = order

  lessons.value.sort((a, b) => {
    if (field === 'Spaces' || field === 'Price') {
      return order === 'asc' ? Number(a[field]) - Number(b[field]) : Number(b[field]) - Number(a[field])
    } else {
      return order === 'asc' ? a[field].localeCompare(b[field]) : b[field].localeCompare(a[field])
    }
  })

  lessons.value = [...lessons.value]

  filteredLessons.value.sort((a, b) => {
    if (field === 'Spaces' || field === 'Price') {
      return order === 'asc' ? Number(a[field]) - Number(b[field]) : Number(b[field]) - Number(a[field])
    } else {
      return order === 'asc' ? a[field].localeCompare(b[field]) : b[field].localeCompare(a[field])
    }
  })

  filteredLessons.value = [...filteredLessons.value]
}

const changeSearchQuery = async (query) => {
  if (!query) {
    filteredLessons.value = []
    searchQuery.value = ''
    return
  }

  const response = await fetch(`https://express-app-xyl5.onrender.com/search?q=${query}`)
  const data = await response.json()
  
  for(let lesson of cart.value) {
    const lessonIndex = data.findIndex((item) => item.id === lesson.id)
    if (lessonIndex !== -1) {
      data[lessonIndex].Spaces-= lesson.quantity
    }
  }

  filteredLessons.value = data

  searchQuery.value = query

  changeSorting(sortField.value, sortOrder.value)
}

const addToCart = (lessonId) => {
  const lesson = lessons.value.find((lesson) => lesson.id === lessonId)
  const existingCartItem = cart.value.find((item) => item.id === lessonId)

  if (existingCartItem) {
    existingCartItem.quantity++
  } else {
    cart.value.push({ id: lesson.id, quantity: 1 })
  }

  lesson.Spaces--

  const filteredLesson = filteredLessons.value.find((lesson) => lesson.id === lessonId)
  filteredLesson.Spaces--

  changeSorting(sortField.value, sortOrder.value)
}

const removeFromCart = () => {}

const checkout = () => {}
</script>

<template>
  <SiteHeader
    siteName="My Awesome Store"
    :cartDisabled="cart.length === 0"
    @toggleCart="toggleCart"
  />

  <SortSearchBar
    :selectedSortField="sortField"
    :selectedSortOrder="sortOrder"
    :changeSort="changeSorting"
    :changeSearchQuery="changeSearchQuery"
  />

  <LessonCard v-for="lesson in (searchQuery? filteredLessons : lessons)" :key="lesson.id" :lesson="lesson" :addToCart="addToCart" />

  <CartPage
    v-if="showCart"
    :cart="cart"
    @removeFromCart="removeFromCart"
    @checkout="checkout"
  />

  <CheckoutModal
    v-if="showModal"
    :title="modalTitle"
    :message="modalMessage"
    :onClose="closeModal"
  />

</template>

<style scoped>
</style>
