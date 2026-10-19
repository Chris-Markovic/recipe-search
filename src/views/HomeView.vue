<script setup>
import MultiSelect from '../components/MultiSelect.vue'
import { ingredients } from '../data/ingredients_list.js'
import RecipeCard from '../components/RecipeCard.vue'
import Recipe from '../components/Recipes.vue'
import { ref } from 'vue'

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
    ignorePantry: true, //Ignores salt, pepper etc.
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
  <div class="container py-4" style="max-width: 500px">
    <h1 class="mb-3">What's in your fridge?</h1>
    <MultiSelect v-model="fridge" :options="ingredients" placeholder="Pick ingredients" />
  </div>
  <button @click="fetch_recipes" type="button" class="btn btn-primary" id="search_recipe_btn">Search Recipes</button>
  <div class="recipe-grid">
    <RecipeCard v-for="recipe in recipe_list" :key="recipe.id" :recipe="recipe" />
  </div>
</template>

<style>
.recipe-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  margin: 1%;
  gap: 5%;
}

@import url(https://fonts.googleapis.com/css?family=Roboto:400,100,900);

html,
body {
  -moz-box-sizing: border-box;
  box-sizing: border-box;
  height: 100%;
  width: 100%;
  background: #FFF;
  font-family: 'Roboto', sans-serif;
  font-weight: 400;
}

.wrapper {
  display: table;
  height: 100%;
  width: 100%;
}

.container-fostrap {
  display: table-cell;
  padding: 1em;
  text-align: center;
  vertical-align: middle;
}

.fostrap-logo {
  width: 100px;
  margin-bottom: 15px
}

h1.heading {
  color: #fff;
  font-size: 1.15em;
  font-weight: 900;
  margin: 0 0 0.5em;
  color: #505050;
}

@media (min-width: 450px) {
  h1.heading {
    font-size: 3.55em;
  }
}

@media (min-width: 760px) {
  h1.heading {
    font-size: 3.05em;
  }
}

@media (min-width: 900px) {
  h1.heading {
    font-size: 3.25em;
    margin: 0 0 0.3em;
  }
}

.card {
  display: block;
  margin-bottom: 2%;
  line-height: 1.42857143;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 5px 0 rgba(0, 0, 0, 0.16), 0 2px 10px 0 rgba(0, 0, 0, 0.12);
  transition: box-shadow .25s;
}

.card:hover {
  box-shadow: 0 8px 17px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
}

.img-card {
  width: 100%;
  height: 200px;
  border-top-left-radius: 2px;
  border-top-right-radius: 2px;
  display: block;
  overflow: hidden;
}

.img-card img {
  width: 100%;
  height: 200px;
  object-fit: cover;
  transition: all .25s ease;
}

.card-content {
  padding: 15px;
  text-align: left;
}

.card-title {
  margin-top: 0px;
  font-weight: 700;
  font-size: 1.65em;
}

.card-title a {
  color: #000;
  text-decoration: none !important;
}

.card-read-more {
  border-top: 1px solid #D4D4D4;
}

.card-read-more a {
  text-decoration: none !important;
  padding: 10px;
  font-weight: 600;
  text-transform: uppercase
}
</style>