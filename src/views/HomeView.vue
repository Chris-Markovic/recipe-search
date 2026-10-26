<script setup>
import { ref } from 'vue'
import MultiSelect from '../components/MultiSelect.vue'
import RecipeCard from '../components/RecipeCard.vue'
import { ingredients } from '../data/ingredients_list.js'

defineOptions({ name: 'HomeView' })

const apiUrl = import.meta.env.VITE_recipe_API_key
const fridge = ref([])
const recipe_list = ref([])

function fetch_recipes() {
  const params = new URLSearchParams({
    includeIngredients: fridge.value.join(','),
    sort: 'max-used-ingredients',
    fillIngredients: true,
    addRecipeInformation: true,
    addRecipeInstructions: true,
    ignorePantry: true,
    number: 10,
    apiKey: apiUrl,
  })
  const url = `https://api.spoonacular.com/recipes/complexSearch?${params}`
  fetch(url)
    .then((response) => response.json())
    .then((data) => {
      recipe_list.value = data.results
    })
    .catch((error) => {
      console.error('Error fetching recipes:', error)
    })
}
</script>

<template>
  <section class="mb-4" style="max-width: 500px; margin-left: 30%;">
    <h1 class="h2 mb-3">What's in your fridge?</h1>
    <MultiSelect v-model="fridge" :options="ingredients" placeholder="Pick ingredients" />
    <button
      @click="fetch_recipes"
      type="button"
      class="btn btn-primary mt-3"
      :disabled="!fridge.length"
    >
      Search recipes
    </button>
  </section>

  <div class="row">
    <div v-for="recipe in recipe_list" :key="recipe.id" class="col-sm-6 col-lg-4 mb-4">
      <RecipeCard :recipe="recipe" />
    </div>
  </div>
</template>
