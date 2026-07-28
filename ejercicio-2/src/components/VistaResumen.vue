<script setup>
import { onMounted, onUnmounted } from 'vue'
onMounted(()   => console.log('[Resumen] montado'))
onUnmounted(() => console.log('[Resumen] DESMONTADO'))
</script>
<template>
  <div>
    <h3>Resumen del vivero</h3>
    <p>4 especies · 20 unidades en bodega</p>
  </div>
</template>