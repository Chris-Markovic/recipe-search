<script setup>
//import {recipe_list} from '../views/HomeView.vue'
import { useRoute } from 'vue-router'
import { ref } from 'vue'


const route = useRoute()
const id = route.params.id
const apiUrl = import.meta.env.VITE_recipe_API_key
const recipe = ref(null)
const url = `https://api.spoonacular.com/recipes/${id}/information?apiKey=${apiUrl}`
fetch(url)
    .then(async (response) => {
        const data = await response.json()
        console.log(data)
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
    <div v-if="recipe">
        <div class="recipe-header">
            <h1>{{ recipe.title }}</h1>
            <img :src="recipe.image" :alt="recipe.name" />
            <h2 style="margin-left:40%; margin-top:15%;">Ingredients:</h2>
        </div>

        <div class="row m-3">
            <div v-for="ingredient in recipe.extendedIngredients" :key="ingredient.name" class="col-auto mb-3">
                <div class="card border border-dark ingredient-card">
                    <img :src="`https://img.spoonacular.com/ingredients_100x100/${ingredient.image}`"
                        :alt="ingredient.name" class="card-img-top ingredient-image" />
                    <div class="card-body">
                        <p class="card-title">{{ ingredient.name }}</p>
                        <p class="card-text">{{ ingredient.original }}</p>
                    </div>
                </div>
            </div>
        </div>

    </div>

</template>

<style>
.recipe-img {
    width: 40%;
    margin-left: 30%;
}

.recipe-header h1 {
    display: block;
    margin-left: 25%;
}

.recipe-header img {
    display: block;
    margin-left: 30%;
}

.ingredient-card {
    width: 18rem;
    height: 24rem;
}

.ingredient-image {
    width: 100%;
    height: 10rem;
    object-fit: contain;
}

.ingredient-card .card-body {
    min-height: 0;
    overflow-y: auto;
}
</style>