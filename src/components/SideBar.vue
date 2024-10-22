<template>
    <div class="sort-sidebar">
      <div class="sort-section">
        <label for="sort-field">Sort by:</label>
        <select id="sort-field" v-model="localSortField">
          <option value="Sport">Sport</option>
          <option value="Location">Location</option>
          <option value="Price">Price</option>
          <option value="Spaces">Spaces</option>
        </select>
      </div>
  
      <div class="sort-section">
        <label for="sort-order">Order:</label>
        <select id="sort-order" v-model="localSortOrder">
          <option value="asc">Ascending</option>
          <option value="desc">Descending</option>
        </select>
      </div>
  
      <button v-on:click="applySort" class="apply-button">Apply Sort</button>
    </div>
  </template>
  
  <script setup>
  import { ref, watch } from 'vue';
  
  const props = defineProps({
    selectedSortField: {
      type: String,
      default: 'Sport'
    },
    selectedSortOrder: {
      type: String,
      default: 'asc'
    }
  });
  
  const emit = defineEmits(['updateSort']);
  
  // Create local copies of props to avoid mutating them directly
  const localSortField = ref(props.selectedSortField);
  const localSortOrder = ref(props.selectedSortOrder);
  
  // Watch props to update local values if they change externally
  watch(() => props.selectedSortField, (newValue) => {
    localSortField.value = newValue;
  });
  watch(() => props.selectedSortOrder, (newValue) => {
    localSortOrder.value = newValue;
  });
  
  // Emit the selected sort options to the parent component
  const applySort = () => {
    emit('updateSort', { field: localSortField.value, order: localSortOrder.value });
  };
  </script>
  
  <style scoped>
  .sort-sidebar {
    padding: 20px;
    background-color: #f4f4f4;
    width: 250px;
    height: 100%;
    top: 0;
    left: 0;
    box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
    color: #333;
    margin-top: 70px;
  }
  
  h2 {
    font-size: 1.5rem;
    margin-bottom: 20px;
  }
  
  .sort-section {
    margin-bottom: 20px;
  }
  
  label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
  }
  
  select {
    width: 100%;
    padding: 8px;
    font-size: 1rem;
    margin-bottom: 10px;
  }
  
  .apply-button {
    padding: 10px 20px;
    background-color: #333;
    color: #fff;
    border: none;
    cursor: pointer;
    width: 100%;
    font-size: 1rem;
  }
  
  .apply-button:hover {
    background-color: #555;
  }
  </style>
  