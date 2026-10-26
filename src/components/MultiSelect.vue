<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

const props = defineProps({
    options: { type: Array, required: true },
    placeholder: { type: String, default: 'Select...' },
})

const selected = defineModel({ default: () => [] })
const search = ref('')
const open = ref(false)
const root = ref(null)

const filtered = computed(() =>
    props.options.filter((o) => o.toLowerCase().includes(search.value.toLowerCase())),
)

const allSelected = computed(
    () => filtered.value.length > 0 && filtered.value.every((o) => selected.value.includes(o)),
)

function toggleAll() {
    if (allSelected.value) {
        selected.value = selected.value.filter((o) => !filtered.value.includes(o))
    } else {
        selected.value = [...new Set([...selected.value, ...filtered.value])]
    }
}

function remove(item) {
    selected.value = selected.value.filter((o) => o !== item)
}

function onClickOutside(e) {
    if (root.value && !root.value.contains(e.target)) open.value = false
}
onMounted(() => document.addEventListener('click', onClickOutside))
onBeforeUnmount(() => document.removeEventListener('click', onClickOutside))
</script>

<template>
    <div ref="root" class="ms">
        <button
            type="button"
            class="btn btn-outline-secondary btn-block dropdown-toggle d-flex align-items-center"
            :aria-expanded="open"
            @click="open = !open"
        >
            <span>{{ placeholder }}</span>
            <span v-if="selected.length" class="badge badge-secondary ml-2">{{ selected.length }}</span>
        </button>

        <div v-if="open" class="dropdown-menu show w-100">
            <div class="px-2 pb-2">
                <input v-model="search" type="search" class="form-control" placeholder="Search ingredients" />
            </div>

            <div class="ms-list">
                <label v-if="filtered.length" class="dropdown-item d-flex align-items-center">
                    <input type="checkbox" :checked="allSelected" @change="toggleAll" />
                    <span class="ml-2">Select all</span>
                </label>

                <label v-for="option in filtered" :key="option" class="dropdown-item d-flex align-items-center">
                    <input v-model="selected" type="checkbox" :value="option" />
                    <span class="ml-2">{{ option }}</span>
                </label>

                <div v-if="!filtered.length" class="dropdown-item-text text-muted">No results</div>
            </div>
        </div>

        <div v-if="selected.length" class="d-flex flex-wrap mt-2">
            <span v-for="item in selected" :key="item" class="badge badge-light border mr-1 mb-1">
                {{ item }}
                <button
                    type="button"
                    class="close ml-1"
                    :aria-label="`Remove ${item}`"
                    @click="remove(item)"
                >
                    <span aria-hidden="true">&times;</span>
                </button>
            </span>
        </div>
    </div>
</template>

<style scoped>
.ms {
    position: relative;
    width: 100%;
}

.ms-list {
    max-height: 250px;
    overflow-y: auto;
}
</style>