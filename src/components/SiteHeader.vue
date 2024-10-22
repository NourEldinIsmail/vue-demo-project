<template>
    <header class="site-header">
      <div class="site-brand">
        <p class="site-name">{{ siteName }}</p>
      </div>
      <button
        class="cart-button"
        v-on:click="$emit('toggleCart')"
        :disabled="props.cartDisabled"
      >
        <span class="cart-text">Cart</span>
        <img
          src="https://img.icons8.com/ios-filled/50/000000/shopping-cart.png"
          alt="Cart"
          class="cart-icon"
        />
      </button>
    </header>
  </template>
  
  <script setup>
  const props = defineProps({
    siteName: {
      type: String,
      default: 'My Website',
    },
    cartDisabled: {
      type: Boolean,
      default: true,
    },
  });
  </script>
  
  <style scoped>
  .site-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 20px;
    background: linear-gradient(45deg, #5A70e0, #7652de);
    color: #fff;
    position: fixed; /* Fixes the header to the top */
    top: 0; /* Positions it at the top */
    left: 0; /* Aligns it to the left */
    right: 0; /* Aligns it to the right */
    z-index: 1000; /* Ensures it stays above other elements */
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2); /* Adds a slight shadow */
  }
  
  .site-brand {
    text-decoration: none;
  }
  
  .site-name {
    color: #fff; /* White text */
    font-size: 2rem;
    margin: 0;
    text-decoration: none; /* Remove underline from link */
  }
  
  .cart-button {
    background: #f4f4f7; /* Updated background color */
    border: none;
    border-radius: 20px; /* Rounded corners */
    cursor: pointer;
    padding: 8px 12px; /* Padding for button */
    display: flex;
    align-items: center;
    opacity: 1; /* Fully visible when enabled */
  }

  .cart-button:hover {
    background: #c1c1c2;
  }
  
  .cart-button:disabled {
    opacity: 0.5; /* Dim button when disabled */
    cursor: not-allowed; /* Change cursor when disabled */
  }
  
  .cart-text {
    margin-right: 5px; /* Space between text and icon */
    font-weight: bold; /* Bold text */
  }
  
  .cart-icon {
    width: 24px; /* Adjusted icon size */
    height: 24px; /* Adjusted icon size */
  }
  </style>
  