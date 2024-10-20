<script setup>
  import { ref, watch } from 'vue'
  import LessonsDisplay from '/src/components/LessonsDisplay.vue'
  import SiteHeader from '/src/components/SiteHeader.vue'
  import SideBar from '/src/components/SideBar.vue'
  import CartPage from '/src/components/CartPage.vue'

  import lessons from './assets/lessons.json'

  const sortField = ref('Sport')
  const sortOrder = ref('asc')
  const lessonsList = ref(lessons)
  const cart = ref([])
  const showCart = ref(false) 

  // Function to update sorting field and order
  const updateSorting = ({ field, order }) => {
    sortField.value = field
    sortOrder.value = order
  }

  const addToCart = (index) => {
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
  const removeFromCart = (index) => {
    const cartItem = cart.value[index]
    cart.value.splice(index, 1)  // Remove item from cart
    const lessonIndex = lessonsList.value.findIndex(item => item.Sport === cartItem.Sport)

    if (lessonIndex !== -1) {
      lessonsList.value[lessonIndex].Spaces += cartItem.quantity  // Restore spaces to lessonsList
    }
  }

  // Function to toggle cart visibility
  const toggleCart = () => {
    showCart.value = !showCart.value
  }

  // Watch for changes in sort field or sort order and sort the lessonsList in place
  watch([sortField, sortOrder, lessonsList], () => {
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
  <SiteHeader siteName="My Awesome Store" @toggleCart="toggleCart" />
  <main>
    <SideBar :selectedSortField="sortField" :selectedSortOrder="sortOrder" @updateSort="updateSorting" />
    <LessonsDisplay :lessons="lessonsList" @addToCart="addToCart"/>
    <CartPage v-if="showCart" :cart="cart" @onRemove="removeFromCart" />
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
