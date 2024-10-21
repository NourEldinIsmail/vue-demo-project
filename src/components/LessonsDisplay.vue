<template>
    <div class="lessons-grid">
        <div v-for="(lesson, index) in lessons.filter(lesson => matchQuery(lesson))" :key="index" class="lesson-card">
            <img :src="lesson.icon" :alt="lesson.Sport + ' icon'" class="lesson-icon" />
            <div class="lesson-details">
                <h2>{{ lesson.Sport }}</h2>
                <p>Location: {{ lesson.Location }}</p>
                <p>Price: {{ lesson.Price }}</p>
                <p>Spaces: {{ lesson.Spaces }}</p>
            </div>

            <button
                v-on:click="emit('addToCart', lesson.id)"
                :disabled="lesson.Spaces === 0"
                class="add-to-cart-button"
            >
                Add to Cart
            </button>
      </div>
    </div>
  </template>
  
  <script setup>
    const props = defineProps({
        lessons: {
            type: Array,
            required: true
        },
        searchQuery: {
            type: String
        }
    })

    const emit = defineEmits(['addToCart'])

    const matchQuery = (lesson) => {
        if(props.searchQuery === ''){
            return true
        }
        return (
            lesson.Sport.toLowerCase().includes(props.searchQuery.toLowerCase()) ||
            lesson.Location.toLowerCase().includes(props.searchQuery.toLowerCase()) ||
            lesson.Price.toLowerCase().includes(props.searchQuery.toLowerCase()) ||
            String(lesson.Spaces).includes(props.searchQuery)
        )
    }
  </script>
  
  <style scoped>
  .lessons-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 20px;
    padding: 20px;
    width: 1200px;
  }
  
  .lesson-card {
    background-color: #fff;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    text-align: center;
    padding: 20px;
    transition: transform 0.3s ease;
  }
  
  .lesson-card:hover {
    transform: scale(1.05);
  }
  
  .lesson-icon {
    width: 80px;
    height: 80px;
    margin-bottom: 10px;
  }
  
  .lesson-details h2 {
    margin: 10px 0;
    font-size: 1.5rem;
    color: #333;
  }
  
  .lesson-details p {
    margin: 5px 0;
    color: #666;
  }

  .add-to-cart-button {
    background-color: #28a745;
    color: #fff;
    border: none;
    padding: 10px 20px;
    cursor: pointer;
    font-size: 1rem;
    border-radius: 5px;
  }

  .add-to-cart-button:disabled {
    background-color: #ccc;
    cursor: not-allowed;
  }

  @media (max-width: 350px) {
    .lessons-grid {
        width: 600px;
    }
  }
  </style>
  