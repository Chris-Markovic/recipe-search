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
        <button type="button" class="ms-toggle" :aria-expanded="open" @click="open = !open">
            <span>{{ placeholder }}</span>
            <span v-if="selected.length" class="ms-count">{{ selected.length }}</span>
            <span class="ms-caret" aria-hidden="true">▾</span>
        </button>

        <div v-if="open" class="ms-menu">
            <div class="ms-search-wrap">
                <input v-model="search" type="search" class="ms-search" placeholder="Search..." />
            </div>

            <div class="ms-list">
                <label v-if="filtered.length" class="ms-item ms-all">
                    <input type="checkbox" :checked="allSelected" @change="toggleAll" />
                    <span>Select all</span>
                </label>

                <label v-for="option in filtered" :key="option" class="ms-item">
                    <input v-model="selected" type="checkbox" :value="option" />
                    <span>{{ option }}</span>
                </label>

                <div v-if="!filtered.length" class="ms-empty">No results</div>
            </div>
        </div>

        <div v-if="selected.length" class="ms-chips">
            <span v-for="item in selected" :key="item" class="ms-chip">
                {{ item }}
                <button type="button" :aria-label="`Remove ${item}`" @click="remove(item)">×</button>
            </span>
        </div>
    </div>
</template>

<style scoped>
.ms {
    position: relative;
    width: 100%;
}

.ms-toggle {
    width: 100%;
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.6rem 0.9rem;
    background: #fff;
    border: 1px solid #ced4da;
    border-radius: 0.5rem;
    font: inherit;
    color: inherit;
    text-align: left;
    cursor: pointer;
}

.ms-toggle:focus-visible {
    outline: 2px solid #86b7fe;
    outline-offset: 1px;
}

.ms-count {
    background: #0d6efd;
    color: #fff;
    border-radius: 999px;
    padding: 0 0.55rem;
    font-size: 0.85rem;
}

.ms-caret {
    margin-left: auto;
}

.ms-menu {
    position: absolute;
    top: calc(100% + 4px);
    left: 0;
    right: 0;
    z-index: 1000;
    background: #fff;
    border: 1px solid #ced4da;
    border-radius: 0.5rem;
    box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);
    overflow: hidden;
}

.ms-search-wrap {
    padding: 0.5rem;
    border-bottom: 1px solid #eee;
}

.ms-search {
    box-sizing: border-box;
    width: 100%;
    padding: 0.45rem 0.7rem;
    border: 1px solid #ced4da;
    border-radius: 0.4rem;
    font: inherit;
}

.ms-list {
    max-height: 250px;
    overflow-y: auto;
    padding: 0.25rem 0;
}

.ms-item {
    display: flex;
    align-items: center;
    gap: 0.6rem;
    padding: 0.45rem 0.9rem;
    margin: 0;
    cursor: pointer;
}

.ms-item:hover {
    background: #f1f3f5;
}

.ms-item input {
    position: static;
    float: none;
    margin: 0;
    width: 1rem;
    height: 1rem;
    flex-shrink: 0;
}

.ms-all {
    font-weight: 600;
    border-bottom: 1px solid #eee;
}

.ms-empty {
    padding: 0.5rem 0.9rem;
    color: #6c757d;
}

.ms-chips {
    display: flex;
    flex-wrap: wrap;
    gap: 0.4rem;
    margin-top: 0.6rem;
}

.ms-chip {
    display: inline-flex;
    align-items: center;
    gap: 0.3rem;
    background: #e7f1ff;
    color: #0a58ca;
    border-radius: 999px;
    padding: 0.2rem 0.4rem 0.2rem 0.7rem;
    font-size: 0.9rem;
}

.ms-chip button {
    border: 0;
    background: transparent;
    color: inherit;
    font-size: 1rem;
    line-height: 1;
    cursor: pointer;
    padding: 0 0.2rem;
}
</style>