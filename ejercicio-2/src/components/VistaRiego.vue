<script setup>
import { ref, onMounted, onUnmounted, watch } from 'vue';

const litros = ref(0);


onMounted(()   => console.log('[Riego] montado'))
/*
onMounted(()   => {
    console.log('[Riego] montado');
    litros.value=localStorage.getItem("litros");
})
*/
onUnmounted(() => console.log('[Riego] DESMONTADO — se perdió el estado'))

/*
watch(litros,(litros)=>{
   localStorage.setItem("litros",litros);
})
*/
</script>

<template>
  <div>
    <h3>Registro de riego</h3>
    <p>Litros aplicados hoy: <strong>{{ litros }}</strong></p>
    <button type="button" @click="litros++">Sumar 1 litro</button>
  </div>
</template>