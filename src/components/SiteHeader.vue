<template>
    <header class="site-header">
      <div class="site-brand">
        <router-link to="/" class="site-name">{{ siteName }}</router-link>
      </div>
      <button class="cart-button" v-on:click="$emit('toggleCart')">
        <img src="https://img.icons8.com/ios-filled/50/000000/shopping-cart.png" alt="Cart" class="cart-icon" />
      </button>
    </header>
  </template>
  
  <script setup>
  defineProps({
    siteName: {
      type: String,
      default: 'My Website'
    }
  })
  </script>
  
  <style scoped>
  .site-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 20px;
    background-color: #333;
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
    background-color: transparent;
    border: none;
    cursor: pointer;
    padding: 5px;
    display: flex;
    align-items: center;
  }
  
  .cart-icon {
    width: 30px;
    height: 30px;
  }
  </style>
  