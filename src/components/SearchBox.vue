<template>
    <div class="search-container">
      <input
        type="text"
        v-model="searchQuery"
        @input="emitSearchQuery"
        placeholder="Search lessons..."
        class="search-input"
      />
    </div>
  </template>
  
  <script setup>
  import { ref } from 'vue'

  const searchQuery = ref("")


  const emit = defineEmits(['updateSearchQuery'])

  const emitSearchQuery = () => {
    emit("updateSearchQuery", searchQuery.value)
  }
  </script>
  
  <style scoped>
  .search-container {
    margin: 20px 0;
    text-align: center;
  }
  
  .search-input {
    width: 80%;
    padding: 10px;
    border-radius: 8px;
    border: 1px solid #ccc;
    font-size: 16px;
  }
  </style>
  