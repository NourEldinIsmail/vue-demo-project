<script setup>
import { ref, watch } from 'vue'
import LessonsDisplay from '/src/components/LessonsDisplay.vue'
import SiteHeader from '/src/components/SiteHeader.vue'
import SideBar from '/src/components/SideBar.vue'
import CartPage from '/src/components/CartPage.vue'
import CheckoutModal from '/src/components/CheckoutModal.vue'
import SearchBox from '/src/components/SearchBox.vue'

import lessons from './assets/lessons.json'

const sortField = ref('None')
const sortOrder = ref('asc')
const lessonsList = ref(lessons)
const cart = ref([])
const showCart = ref(false) 
const showModal = ref(false)  // To control the modal
const modalMessage = ref('')  // To store modal message
const modalTitle = ref('')    // To store modal title
const searchQuery = ref('')  // Store search query

// Function to update sorting field and order
const updateSorting = ({ field, order }) => {
  sortField.value = field
  sortOrder.value = order
}

// Function to update the search query and filter the lessons
const updateSearchQuery = (query) => {
  searchQuery.value = query
}

const addToCart = (lessonId) => {
  const index = lessonsList.value.findIndex(lesson => lesson.id === lessonId)
  if(index === -1)
    return

  const updatedLessons = [...lessonsList.value]
  if (updatedLessons[index].Spaces > 0) {
    updatedLessons[index].Spaces -= 1

    // Check if the lesson is already in the cart
    const cartIndex = cart.value.findIndex(item => item.Sport === updatedLessons[index].Sport)

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
const removeFromCart = (lessonId) => {
  const index = lessonsList.value.findIndex(lesson => lesson.id === lessonId)
  if(index === -1)
    return

  const cartItem = cart.value[index]
  cart.value.splice(index, 1)  // Remove item from cart
  const lessonIndex = lessonsList.value.findIndex(item => item.Sport === cartItem.Sport)

  if (lessonIndex !== -1) {
    lessonsList.value[lessonIndex].Spaces += cartItem.quantity  // Restore spaces to lessonsList
  }

  if(cart.value.length === 0){
    toggleCart()
  }
}

// Function to toggle cart visibility
const toggleCart = () => {
  showCart.value = !showCart.value
}

// Function to handle checkout
const checkout = (customerName, customerPhone) => {
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

// Watch for changes in sort field or sort order and sort the lessonsList in place
watch([sortField, sortOrder, lessonsList], () => {
  if(sortField.value == 'None')
    return

  lessonsList.value.sort((a, b) => {
    let compareA = a[sortField.value]
    let compareB = b[sortField.value]

    // Handle numeric sorting for fields like Price and Spaces
    if (sortField.value === 'Price') {
      compareA = parseInt(compareA.replace('$', '')) || compareA
      compareB = parseInt(compareB.replace('$', '')) || compareB
    }
    else if(sortField.value === 'Spaces'){
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
  <SiteHeader siteName="My Awesome Store" :cartDisabled="cart.length === 0" @toggleCart="toggleCart" />
  <main>
    <SideBar :selectedSortField="sortField" :selectedSortOrder="sortOrder" @updateSort="updateSorting" />
    <SearchBox @updateSearchQuery="updateSearchQuery" />
    <LessonsDisplay :lessons="lessonsList" :searchQuery="searchQuery" @addToCart="addToCart"/>
    <CartPage v-if="showCart" :cart="cart" @removeFromCart="removeFromCart" @checkout="checkout"/>
    <CheckoutModal v-if="showModal" :title="modalTitle" :message="modalMessage" :onClose="closeModal" />
  </main>
</template>

<style scoped>
/* Same styles as before */
</style>
