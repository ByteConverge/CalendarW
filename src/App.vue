<template>
  <div class="min-h-screen bg-gray-100 p-6">
    <div class="max-w-md mx-auto bg-white rounded-lg shadow p-4">
      <h1 class="text-xl font-semibold mb-4">Calendar</h1>
      <Calendar v-model="selected" />
    
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Calendar from './components/Calendar.vue'

// const selected = ref(null)
</script>
