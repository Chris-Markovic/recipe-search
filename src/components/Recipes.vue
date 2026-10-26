<script setup>
import { RouterLink, useRoute } from 'vue-router'
import { ref } from 'vue'

defineOptions({ name: 'RecipeDetail' })

const route = useRoute()
const id = route.params.id
const apiUrl = import.meta.env.VITE_recipe_API_key
const recipe = ref(null)
const url = `https://api.spoonacular.com/recipes/${id}/information?apiKey=${apiUrl}`
fetch(url)
    .then(async (response) => {
        const data = await response.json()
        if (!response.ok) {
            throw new Error(data.message || `Request failed with status ${response.status}`)
        }
        recipe.value = data
    })
    .catch((error) => {
        console.error('Error fetching recipes:', error)
    })
</script>

<template>
    <div>
        <RouterLink class="btn btn-outline-secondary mb-3" :to="{ name: 'home' }">
            &larr; Back to recipes
        </RouterLink>

        <div v-if="recipe">
            <div class="row mb-4">
                <div class="col-md-6">
                    <img :src="recipe.image" :alt="recipe.title" class="img-fluid rounded recipe-image" />
                </div>
                <div class="col-md-6">
                    <h1>{{ recipe.title }}</h1>
                </div>
            </div>
            <h2 class="h3 mb-3">Ingredients</h2>
            <div class="row">
                <div v-for="ingredient in recipe.extendedIngredients" :key="ingredient.name"
                    class="col-sm-6 col-lg-4 mb-3">
                    <div class="card h-100">
                        <img :src="`https://img.spoonacular.com/ingredients_100x100/${ingredient.image}`"
                            :alt="ingredient.name" class="card-img-top ingredient-image" />
                        <div class="card-body">
                            <h3 class="h5 card-title">{{ ingredient.name }}</h3>
                            <p class="card-text">{{ ingredient.original }}</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.recipe-image {
    max-height: 350px;
    object-fit: cover;
}
.ingredient-image {
    height: 140px;
    object-fit: contain;
}
</style>