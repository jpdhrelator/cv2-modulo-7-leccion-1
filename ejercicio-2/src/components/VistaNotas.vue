<script setup>
import { ref } from 'vue'
const nota = ref('')
</script>
<template>
  <div>
    <h3>Notas del día</h3>
    <textarea v-model="nota" rows="4" placeholder="Escribe aquí..."></textarea>
    <p>{{ nota.length }} caracteres</p>
  </div>
</template>

<style scoped>
textarea { width:100%; padding:0.5rem; border:1px solid #e2e6ea; border-radius:8px; }
</style>