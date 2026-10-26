<script setup>
import { RouterLink } from 'vue-router'

defineProps({
  recipe: { type: Object, required: true },
})
</script>

<template>
  <div class="card h-100">
    <img :src="recipe.image" class="card-img-top recipe-image" :alt="recipe.title" />
    <div class="card-body">
      <h2 class="h5 card-title">{{ recipe.title }}</h2>
      <RouterLink
        class="btn btn-secondary"
        :to="{ name: 'recipe', params: { id: recipe.id } }"
      >
        See details
      </RouterLink>
    </div>
  </div>
</template>

<style scoped>
.recipe-image {
  height: 200px;
  object-fit: cover;
}
</style>
