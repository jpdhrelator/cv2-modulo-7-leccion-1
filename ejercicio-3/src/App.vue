<script setup>
import { ref } from 'vue'
import MonitorInvernadero from './components/MonitorInvernadero.vue'
const visible = ref(true)

</script>

<template>
  <button type="button" @click="visible = !visible">
    {{ visible ? 'Apagar' : 'Encender' }} monitor
  </button>
  <MonitorInvernadero v-if="visible" />
</template>
