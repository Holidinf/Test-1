<template>
  <div class="p-4">
    <h1 class="text-2xl font-bold mb-4">Products</h1>
    <button @click="openDialog(null)" class="mb-4 px-4 py-2 text-white bg-blue-500 rounded-md hover:bg-blue-600">Add Product</button>
    <div v-if="loading" class="text-center">Loading...</div>
    <div v-if="error" class="text-center text-red-500">{{ error }}</div>
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
      <div v-for="product in products" :key="product.id" class="p-4 bg-white rounded-md shadow-md">
        <h2 class="text-xl font-semibold">{{ product.title }}</h2>
        <p>{{ product.description }}</p>
        <p class="mt-2 font-bold">${{ product.price }}</p>
        <button @click="openDialog(product)" class="mt-2 px-4 py-2 text-white bg-green-500 rounded-md hover:bg-green-600">Edit</button>
      </div>
    </div>
    <transition name="dialog">
      <div v-if="showDialog" class="fixed inset-0 flex items-center justify-center bg-gray-800 bg-opacity-75">
        <div class="bg-white rounded-md p-6 w-full max-w-lg">
          <h2 class="text-xl font-bold mb-4">{{ dialogProduct ? 'Edit Product' : 'Add Product' }}</h2>
          <form @submit.prevent="saveProduct">
            <div>
              <label class="block text-sm">Title</label>
              <input type="text" v-model="formData.title" class="w-full px-4 py-2 mt-1 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-400" required />
            </div>
            <div>
              <label class="block text-sm">Description</label>
              <textarea v-model="formData.description" class="w-full px-4 py-2 mt-1 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-400" required></textarea>
            </div>
            <div>
              <label class="block text-sm">Price</label>
              <input type="number" v-model="formData.price" class="w-full px-4 py-2 mt-1 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-400" required />
            </div>
            <div class="flex items-center justify-end mt-4">
              <button type="button" @click="closeDialog" class="px-4 py-2 text-gray-700 bg-gray-200 rounded-md hover:bg-gray-300 mr-2">Cancel</button>
              <button type="submit" class="px-4 py-2 text-white bg-blue-500 rounded-md hover:bg-blue-600">Save</button>
            </div>
          </form>
        </div>
      </div>
    </transition>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const products = ref([])
const loading = ref(true)
const error = ref(null)
const showDialog = ref(false)
const dialogProduct = ref(null)
const formData = ref({
  title: '',
  description: '',
  price: ''
})

const fetchProducts = async () => {
  try {
    const response = await fetch('https://dummyjson.com/products')
    const data = await response.json()
    products.value = data.products
  } catch (err) {
    error.value = 'Failed to load products'
  } finally {
    loading.value = false
  }
}

const openDialog = (product) => {
  if (product) {
    dialogProduct.value = product
    formData.value = { ...product }
  } else {
    dialogProduct.value = null
    formData.value = { title: '', description: '', price: '' }
  }
  showDialog.value = true
}

const closeDialog = () => {
  showDialog.value = false
}

const saveProduct = async () => {
  const method = dialogProduct.value ? 'PUT' : 'POST'
  const url = dialogProduct.value 
    ? `https://dummyjson.com/products/${dialogProduct.value.id}`
    : 'https://dummyjson.com/products/add'
  try {
    const response = await fetch(url, {
      method,
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(formData.value)
    })
    const data = await response.json()
    if (method === 'POST') {
      products.value.push(data)
    } else {
      const index = products.value.findIndex(p => p.id === data.id)
      products.value[index] = data
    }
    closeDialog()
  } catch (err) {
    console.error('Failed to save product:', err)
  }
}

onMounted(fetchProducts)
</script>

<style scoped>
/* Add any additional styling here */
