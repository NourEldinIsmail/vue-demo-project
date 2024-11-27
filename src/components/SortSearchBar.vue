<template>
  <div class="sort-search-bar">
    <div class="search-section">
      <input
        type="text"
        id="search-query"
        v-model="localSearchQuery"
        v-on:input="props.changeSearchQuery(localSearchQuery)"
        placeholder="Search lessons..."
      />
    </div>

    <div class="sort-section">
      <select id="sort-field" v-model="localSortField">
        <option disabled value="">Field</option>
        <option value="Sport">Sport</option>
        <option value="Location">Location</option>
        <option value="Price">Price</option>
        <option value="Spaces">Spaces</option>
      </select>
    </div>

    <div class="sort-section">
      <select id="sort-order" v-model="localSortOrder">
        <option disabled value="">Order</option>
        <option value="asc">Ascending</option>
        <option value="desc">Descending</option>
      </select>
    </div>

    <button
      v-on:click="changeSort(localSortField, localSortOrder)"
      class="apply-button"
    >
      Apply
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const props = defineProps({
  changeSort: {
    type: Function,
    required: true,
  },
  changeSearchQuery: {
    type: Function,
    required: true,
  },
})

const localSortField = ref('')
const localSortOrder = ref('')
const localSearchQuery = ref('')
</script>

<style scoped>
.sort-search-bar {
  padding: 20px;
  background-color: #333;
  width: 100%;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  color: #ffffff;
  margin-top: 70px;
  display: flex;
  flex-direction: row;
  align-items: center;
  border-radius: 0 0 15px 15px;
}

.search-section {
  flex: 1;
  margin-right: 20px;
}

.sort-section {
  margin-right: 20px;
}

label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

input,
select {
  width: 100%;
  padding: 8px;
  font-size: 1rem;
  border-radius: 5px;
}

.apply-button {
  padding: 10px 20px;
  background-color: #fff;
  color: #000000;
  border: none;
  cursor: pointer;
  font-size: 1rem;
  border-radius: 5px;
}

.apply-button:hover {
  background-color: #555;
}

@media (max-width: 1110px) {
  .sort-search-bar {
    flex-direction: column;
    gap: 10px;
    align-items: center;
  }
  .sort-search-bar > * {
    width: 100%;
    align-items: center;
    margin-right: 0;
  }
}
</style>
