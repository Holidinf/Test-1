<template>
  <div class="flex items-center justify-center min-h-screen bg-gray-100">
    <div class="w-full max-w-md p-8 space-y-6 bg-white rounded-md shadow-md">
      <h2 class="text-2xl font-bold text-center">Login</h2>
      <form @submit.prevent="login">
        <div>
          <label class="block text-sm">Username</label>
          <input type="text" v-model="username" class="w-full px-4 py-2 mt-1 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-400" />
        </div>
        <div>
          <label class="block text-sm">Password</label>
          <input type="password" v-model="password" class="w-full px-4 py-2 mt-1 border rounded-md focus:outline-none focus:ring-2 focus:ring-blue-400" />
        </div>
        <div class="flex items-center justify-between mt-4">
          <button type="submit" class="px-4 py-2 text-white bg-blue-500 rounded-md hover:bg-blue-600 focus:outline-none focus:ring-2 focus:ring-blue-400">Login</button>
        </div>
      </form>
      <p v-if="errorMessage" class="mt-4 text-sm text-red-500">{{ errorMessage }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const username = ref('')
const password = ref('')
const errorMessage = ref('')
const router = useRouter()

const login = async () => {
  try {
    const response = await fetch('https://dummyjson.com/auth/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        username: username.value,
        password: password.value,
      })
    })
    const data = await response.json()
    if (data.token) {
      localStorage.setItem('token', data.token)
      router.push('/products')
    } else {
      errorMessage.value = 'Invalid username/password'
    }
  } catch (error) {
    console.error('Error:', error)
    errorMessage.value = 'An error occurred while logging in'
  }
}
</script>

<style scoped>
/* Add any additional styling here */
</style>
