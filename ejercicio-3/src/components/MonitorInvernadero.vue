<script setup>
import {
  ref, computed, watch, onBeforeMount, onMounted,
  onBeforeUpdate, onUpdated, onBeforeUnmount, onUnmounted, nextTick
} from 'vue'
const temperatura = ref(null);
const humedad     = ref(null);
const historial   = ref([]);
const cargando    = ref(true);

const listaRef = ref(null)
let intervalo = null

// ---- FASE 1: el cuerpo del setup (equivale a created) ----
// Los datos ya existen y son reactivos, pero el DOM NO. Ni lo intentes.
console.log(`[setup] listaRef vale:${ listaRef.value}`) // null

onBeforeMount(() => {
  console.log(`[onBeforeMount] a punto de entrar al DOM. listaRef ${ listaRef.value}`) // sigue null
});

// ---- FASE 2: montado. El DOM existe. ----
onMounted(async () => {
  console.log(`[onMounted] DOM listo. listaRef: ${listaRef.value}`) // ¡ahora sí!

  // Simulamos una carga inicial asíncrona (como una llamada a API)
  await new Promise(resolve => setTimeout(resolve, 800))
  temperatura.value = 22.5
  humedad.value = 61
  cargando.value = false

  // Efectos secundarios: se ENCIENDEN aquí...
  intervalo = setInterval(() => {
    temperatura.value = +(18 + Math.random() * 10).toFixed(1)
    humedad.value     = Math.round(45 + Math.random() * 35)
    historial.value.push({
      hora: new Date().toLocaleTimeString('es-CL'),
      temp: temperatura.value
    })
  }, 2000)

   window.addEventListener('resize', alRedimensionar)
});

function alRedimensionar() {
  console.log('[resize] ancho:', window.innerWidth)
}

// ---- FASE 3: actualizaciones ----
onBeforeUpdate(() => {
  console.log('[onBeforeUpdate] el DOM todavía muestra lo viejo')
})
onUpdated(() => {
  console.log('[onUpdated] el DOM ya está sincronizado')
  // ⚠️ NUNCA modifiques un ref reactivo aquí sin condición:
  // provocarías otro update → otro onUpdated → bucle infinito.
});

// watch: reacciona a UN dato concreto. Suele ser mejor que onUpdated,
// porque onUpdated se dispara por CUALQUIER cambio del componente.
watch(temperatura, async (nueva, anterior) => {
  if (anterior !== null && Math.abs(nueva - anterior) > 5) {
    console.warn(`[watch] salto brusco: ${anterior}° → ${nueva}°`)
  }
  // nextTick espera a que Vue termine de actualizar el DOM
  await nextTick()
  if (listaRef.value) {
    listaRef.value.scrollTop = listaRef.value.scrollHeight
  }
})
const estado = computed(() => {
  if (temperatura.value === null) return 'sin-datos'
  if (temperatura.value > 26) return 'caluroso'
  if (temperatura.value < 20) return 'frio'
  return 'optimo'
});

// ---- FASE 4: destrucción. LIMPIEZA OBLIGATORIA. ----
onBeforeUnmount(() => {
  console.log('[onBeforeUnmount] aún puedo leer el DOM')
});

onUnmounted(() => {
  clearInterval(intervalo);
  window.removeEventListener('resize', alRedimensionar)
  console.log('[onUnmounted] limpio. Sin fugas de memoria.')
})
</script>


<template>
  <section class="monitor" :class="`monitor--${estado}`">
    <h3>Monitor del invernadero</h3>

    <!-- Loading state: nunca dejes la UI en blanco -->
    <div v-if="cargando" class="skeleton">
      <div class="linea"></div>
      <div class="linea corta"></div>
    </div>

    <template v-else>
      <p class="lectura">{{ temperatura }}°C · {{ humedad }}% HR</p>
      <p class="estado">Estado: {{ estado }}</p>

      <ul ref="listaRef" class="historial">
        <li v-for="(l, i) in historial" :key="i">
          {{ l.hora }} — {{ l.temp }}°C
        </li>
      </ul>
    </template>
  </section>
</template>

<style scoped>
.monitor { border:1px solid #e2e6ea; border-left-width:4px; border-radius:12px; padding:1.5rem; background:#fff; }
.monitor--optimo   { border-left-color:#42b883; }
.monitor--caluroso { border-left-color:#c0392b; }
.monitor--frio     { border-left-color:#3498db; }
.monitor--sin-datos{ border-left-color:#d5dade; }

.lectura { font-size:1.5rem; font-weight:700; margin:0.5rem 0; }
.estado  { color:#6b7680; margin:0 0 1rem; }
.historial { max-height:150px; overflow-y:auto; list-style:none; margin:0; padding:0.5rem; background:#f6f7f9; border-radius:8px; font-size:0.875rem; }
.historial li { padding:0.25rem 0; }

/* Skeleton loader — mejor que un spinner: no salta el layout */
.skeleton .linea { height:1rem; background:#e2e6ea; border-radius:4px; margin-bottom:0.5rem; animation:pulso 1.5s infinite; }
.skeleton .corta { width:60%; }
@keyframes pulso { 0%,100%{opacity:1} 50%{opacity:0.5} }
</style>


