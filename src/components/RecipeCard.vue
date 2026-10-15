<script setup>
const { recipe } = defineProps({
    recipe: { type: Object, required: true },
})
</script>


<template>
    <div class="card">
        <img :src="recipe.image" class="card-img-top" alt="Recipe photo" />
        <div class="card-content">
            <h4 class="card-title">{{ recipe.title }}</h4>
            <p class="see-more">
                <RouterLink :to="{ name: 'recipe', params: { id: recipe.id } }">
                    <button type="button" class="btn btn-secondary">See details</button>
                </RouterLink>
            </p>
        </div>
    </div>
</template>

<style>
.see-more {
    margin-bottom: 5px;
}
</style>