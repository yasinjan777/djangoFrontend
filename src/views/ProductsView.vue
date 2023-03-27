<script setup>
import { onMounted, ref } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import axios from 'axios'
import categories from './categories.json'
import CategoryCarusel from '../components/CategoryCarusel.vue'

const router = useRouter()
const route = useRoute()
const isLoaded = ref(false)
const products = ref([])

const loadData = async () => {
  isLoaded.value = false
  const product_req = await axios.get('http://localhost:8000/api/products', {
    params: {
      category: route.query.category || 1
    }
  })
  products.value = product_req.data.results
  isLoaded.value = true
}

onMounted(async () => {
  await loadData()
})

const addQuery = async (key, value) => {
  const query = { ...route.query }
  query[key] = value
  await router.push({ query })
  loadData()
}
</script>

<template>
  <!-- <CategoryCarusel /> -->
  <div>
    <ul v-for="category in categories" :key="category.id">
      <li>
        <button @click="addQuery('category', category.id)">{{ category.title }}</button>
      </li>
    </ul>
  </div>
  <h1>Products</h1>
  <div v-if="isLoaded">
    <ul>
      <li v-for="product in products" :key="product.id">
        <img :src="product.image" :alt="product.title" />
        <p>{{ product.title }}</p>
      </li>
    </ul>
  </div>
  <div v-else>
    <p>Loading...</p>
  </div>
</template>
