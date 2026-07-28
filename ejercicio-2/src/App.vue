<script setup>
import { ref, shallowRef, computed } from 'vue'
import VistaResumen from './components/VistaResumen.vue'
import VistaRiego from './components/VistaRiego.vue'
import VistaNotas from './components/VistaNotas.vue'

const pestanas = [
  { clave: 'resumen', etiqueta: 'Resumen', componente: VistaResumen },
  { clave: 'riego',   etiqueta: 'Riego',   componente: VistaRiego },
  { clave: 'notas',   etiqueta: 'Notas',   componente: VistaNotas }
];

const claveActiva = ref('resumen');


const componenteActivo = computed(
  () => pestanas.find(p => p.clave === claveActiva.value).componente
);


</script>

<template>
  <main class="panel">
    <h1>Gestión del vivero</h1>
    <nav class="tabs" role="tablist">
      <button
        v-for="tab in pestanas"
        :key="tab.clave"
        type="button"
        role="tab"
        :aria-selected="claveActiva === tab.clave"
        :class="{ activa: claveActiva === tab.clave }"
        @click="claveActiva = tab.clave"
      >
        {{ tab.etiqueta }}
      </button>
    </nav>
     <div class="contenido">
       <KeepAlive>
         <component :is="componenteActivo" />
       </KeepAlive>
     </div>
  </main>
</template>
<style scoped>
.panel { max-width:720px; margin:0 auto; padding:1.5rem; }
.tabs { display:flex; gap:0.5rem; border-bottom:2px solid #e2e6ea; margin-bottom:1.5rem; }
.tabs button {
  min-height:44px; padding:0 1rem; border:none; background:none;
  cursor:pointer; font-weight:600; color:#6b7680; border-bottom:2px solid transparent;
  margin-bottom:-2px;
}
.tabs button.activa { color:#2f8f66; border-bottom-color:#42b883; }
.contenido { border:1px solid #e2e6ea; border-radius:12px; padding:1.5rem; background:#fff; }
</style>