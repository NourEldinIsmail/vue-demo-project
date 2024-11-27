<template>
  <div class="card">
    <h1>{{ lesson.Sport }}</h1>

    <div class="content">
      <div class="info">
        <p>Location: {{ lesson.Location }}</p>
        <p>Price: ${{ lesson.Price }}</p>
        <p>Spaces: {{ lesson.Spaces }}</p>
      </div>
      <img :src="lesson.icon" :alt="lesson.Sport + ' image'" class="image" />
    </div>

    <button
      v-on:click="props.addToCart(lesson.id)"
      :disabled="lesson.Spaces === 0"
      class="add-button"
    >
      +
    </button>
  </div>
</template>

<script setup>
const props = defineProps({
  lesson: {
    type: Object,
    required: true,
  },
  addToCart: {
    type: Function,
    required: true,
  },
})
</script>

<style scoped>
.card {
  position: relative;
  background-color: #333;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  overflow: hidden;
  text-align: center;
  padding: 20px;
  margin-top: 10px;
}

.image {
  width: 120px;
  height: 120px;
  margin-bottom: 10px;
}

h1 {
  margin: 10px 0;
  font-size: 3rem;
  color: #ffffff;
}

.content {
  display: flex;
  justify-content: center;
  gap: 40px;
}

.info {
  text-align: left;
}

.info p {
  margin: 5px 0;
  color: #ffffff;
  font-size: 1.2rem;
}

.add-button {
  background-color: #ffffff;
  color: #000000;
  cursor: pointer;
  font-size: 1.5rem;
  border-radius: 20px;
  width: 40px;
  height: 40px;
  border: none;
  position: absolute;
  bottom: 15px;
  right: 15px;
}

.add-button:hover {
  background-color: #f4f4f7;
  scale: 1.1;
}

.add-button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>
