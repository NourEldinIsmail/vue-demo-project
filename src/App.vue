<script setup>
import { ref, watch } from 'vue'
import LessonsDisplay from '/src/components/LessonsDisplay.vue'
import SiteHeader from '/src/components/SiteHeader.vue'
import SideBar from '/src/components/SideBar.vue'
import CartPage from '/src/components/CartPage.vue'
import CheckoutModal from '/src/components/CheckoutModal.vue'
import SearchBox from '/src/components/SearchBox.vue'

const sortField = ref('None')
const sortOrder = ref('asc')
const lessonsList = ref([])
const cart = ref([])
const showCart = ref(false)
const showModal = ref(false) // To control the modal
const modalMessage = ref('') // To store modal message
const modalTitle = ref('') // To store modal title
const searchQuery = ref('') // Store search query

// Function to update sorting field and order
const updateSorting = ({ field, order }) => {
  sortField.value = field
  sortOrder.value = order
}

// Function to update the search query and filter the lessons
const updateSearchQuery = query => {
  searchQuery.value = query
  fetchLessons()
}

const addToCart = lessonId => {
  const index = lessonsList.value.findIndex(lesson => lesson.id === lessonId)
  if (index === -1) return

  const updatedLessons = [...lessonsList.value]
  if (updatedLessons[index].Spaces > 0) {
    updatedLessons[index].Spaces -= 1

    // Check if the lesson is already in the cart
    const cartIndex = cart.value.findIndex(
      item => item.Sport === updatedLessons[index].Sport,
    )

    if (cartIndex !== -1) {
      // Increase quantity if it's already in the cart
      cart.value[cartIndex].quantity += 1
    } else {
      // Add new item to cart with quantity 1
      cart.value.push({ ...updatedLessons[index], quantity: 1 })
    }
  }
  lessonsList.value = updatedLessons
}

// Function to remove an item from the cart
const removeFromCart = index => {
  const cartItem = cart.value[index]
  cart.value.splice(index, 1)
  const lessonIndex = lessonsList.value.findIndex(
    item => item.id === cartItem.id,
  )

  if (lessonIndex !== -1) {
    lessonsList.value[lessonIndex].Spaces += cartItem.quantity
  }

  if (cart.value.length === 0) {
    toggleCart()
  }
}

// Function to toggle cart visibility
const toggleCart = () => {
  showCart.value = !showCart.value
}

// Function to handle checkout
const checkout = async (customerName, customerPhone) => {

  try {
    const response = await fetch('https://express-app-xyl5.onrender.com/order', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        name: customerName,
        phone: customerPhone,
        lessons: cart.value,
      }),
    })
    if (!response.ok) {
      throw new Error('Error placing order')
    }
  } catch (error) {
    console.error('Error placing order:', error)
    modalTitle.value = 'Error'
    modalMessage.value = 'An error occurred while placing your order. Please try again later.'
    showModal.value = true
    return
  }

  

  try {
    for (const lesson of cart.value) {
      let index = lessonsList.value.findIndex(item => item.id === lesson.id)
      const response = await fetch(`https://express-app-xyl5.onrender.com/lesson/${lesson.id}`, {
        method: 'PUT',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          Spaces: lessonsList.value[index].Spaces,
        }),
      })
      if (!response.ok) {
        throw new Error('Error updating lessons')
      }
    }
  } catch (error) {
    console.error('Error updating lessons:', error)
    modalTitle.value = 'Error'
    modalMessage.value = 'An error occurred while updating lessons. Please try again later.'
    showModal.value = true
    return
  }

  cart.value = []
  toggleCart()

  // Show modal after successful checkout
  modalTitle.value = 'Checkout Successful'
  modalMessage.value = `Thank you, ${customerName}. Your order is confirmed! with following number: ${customerPhone}`
  showModal.value = true
}

// Close modal function
const closeModal = () => {
  showModal.value = false
}

// Function to fetch lessons from the API
const fetchLessons = () => {
  fetch(`https://express-app-xyl5.onrender.com/lessons?search=${searchQuery.value}`)
    .then(response => response.json())
    .then(data => {
      lessonsList.value = data
    })
    .catch(error => {
    console.error('Error fetching lessons:', error)
    })

  for (const item of cart.value) {
    let index = lessonsList.value.findIndex(lesson => lesson.id === item.id)
    if (index !== -1) {
      lessonsList.value[index].Spaces -= item.quantity
    }
  }
}

// Initial fetch of lessons when the component is mounted
fetchLessons()

// Watch for changes in sort field or sort order and sort the lessonsList in place
watch([sortField, sortOrder, lessonsList], () => {
  if (sortField.value == 'None') return

  lessonsList.value.sort((a, b) => {
    let compareA = a[sortField.value]
    let compareB = b[sortField.value]

    // Handle numeric sorting for fields like Price and Spaces
    if (sortField.value === 'Price') {
      compareA = parseInt(compareA.replace('$', '')) || compareA
      compareB = parseInt(compareB.replace('$', '')) || compareB
    } else if (sortField.value === 'Spaces') {
      compareA = parseInt(compareA) || compareA
      compareB = parseInt(compareB) || compareB
    }

    if (sortOrder.value === 'asc') {
      return compareA > compareB ? 1 : -1
    } else {
      return compareA < compareB ? 1 : -1
    }
  })
})
</script>

<template>
  <SiteHeader
    siteName="My Awesome Store"
    :cartDisabled="cart.length === 0"
    @toggleCart="toggleCart"
  />
  <SideBar
    :selectedSortField="sortField"
    :selectedSortOrder="sortOrder"
    @updateSort="updateSorting"
  />
  <main>
    <SearchBox @updateSearchQuery="updateSearchQuery" />
    <LessonsDisplay
      :lessons="lessonsList"
      @addToCart="addToCart"
    />
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
  </main>
</template>

<style scoped>
/* Same styles as before */
</style>
