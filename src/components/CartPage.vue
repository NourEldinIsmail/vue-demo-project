<template>
  <div class="cart-overlay">
    <div class="cart-content">
      <h2>Your Cart</h2>
      <ul class="cart-list">
        <li v-for="(item, index) in cart" :key="index" class="cart-item">
          <span class="cart-item-name">{{ item.Sport }} - Quantity: {{ item.quantity }}</span>
          <button @click="removeItem(index)" class="remove-button">Remove</button>
        </li>
      </ul>

      <div class="checkout-form">
        <h3>Checkout</h3>
        <label for="name">Name:</label>
        <input type="text" id="name" v-model="customerName" placeholder="Enter your name" />

        <label for="phone">Phone:</label>
        <input type="tel" id="phone" v-model="customerPhone" placeholder="Enter your phone" />

        <button @click="checkout" class="checkout-button">Checkout</button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
const props = defineProps({
  cart: {
    type: Array,
    required: true
  },
  onRemove: {
    type: Function,
    required: true
  }
})

const customerName = ref('')
const customerPhone = ref('')

// Function to handle removing an item from the cart
const removeItem = (index) => {
  props.onRemove(index)
}

// Function to handle checkout
const checkout = () => {
  alert(`Checkout successful for ${customerName.value} with phone ${customerPhone.value}`)
}
</script>

<style scoped>
.cart-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.7);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
  color: #333;
}

.cart-content {
  background: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  width: 90%;
  max-width: 500px;
}

h2 {
  margin-bottom: 1rem;
  font-size: 1.5rem;
  color: #333;
  text-align: center;
}

.cart-list {
  list-style-type: none;
  padding: 0;
  margin: 1rem 0;
}

.cart-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem 1rem;
  border-bottom: 1px solid #ccc;
}

.cart-item-name {
  font-size: 1rem;
  color: #555;
}

.remove-button {
  background-color: #ff4d4d;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.remove-button:hover {
  background-color: #ff1a1a;
}

.checkout-form {
  margin-top: 1.5rem;
}

.checkout-form h3 {
  margin-bottom: 0.5rem;
}

label {
  display: block;
  margin: 0.5rem 0 0.2rem;
  font-weight: bold;
}

input[type="text"],
input[type="tel"] {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-bottom: 1rem;
}

.checkout-button {
  background-color: #4CAF50;
  color: white;
  border: none;
  padding: 0.5rem 1rem;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
  width: 100%;
}

.checkout-button:hover {
  background-color: #45a049;
}
</style>