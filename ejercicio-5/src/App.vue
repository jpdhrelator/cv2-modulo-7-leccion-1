<script setup>
import { ref, shallowRef, computed, watch, onMounted } from 'vue'
import PanelBase from './components/PanelBase.vue'
import VistaCatalogo from './components/VistaCatalogo.vue'
import VistaEstadisticas from './components/VistaEstadisticas.vue'

// ============ ESTADO: el padre es el ÚNICO dueño de los datos ============
const plantas = ref([])
const destacadas = ref([])
const escalaTexto = ref(16)
const mensaje = ref(null)

// ============ COMPONENTES DINÁMICOS ============
const pestanas = [
  { clave:'catalogo',     etiqueta:'Catálogo',     componente: VistaCatalogo },
  { clave:'estadisticas', etiqueta:'Estadísticas', componente: VistaEstadisticas }
]
const pestanaActiva = ref('catalogo')
const componenteActivo = computed(
  () => pestanas.find(p => p.clave === pestanaActiva.value).componente
)

// ============ CICLO DE VIDA: carga inicial ============
onMounted(async () => {
  await new Promise(r => setTimeout(r, 400))   // simula fetch a una API
  plantas.value = [
    { id:1, nombre:'Monstera Deliciosa', precio:12990, luz:'Indirecta', stock:3,  diasSinRiego:2 },
    { id:2, nombre:'Sansevieria',        precio:8500,  luz:'Baja',      stock:0,  diasSinRiego:11 },
    { id:3, nombre:'Potus',              precio:5990,  luz:'Indirecta', stock:12, diasSinRiego:7 },
    { id:4, nombre:'Cactus San Pedro',   precio:15900, luz:'Directa',   stock:5,  diasSinRiego:1 },
    { id:5, nombre:'Helecho Boston',     precio:7490,  luz:'Indirecta', stock:8,  diasSinRiego:9 }
  ]
})

// ============ HANDLERS de los eventos que suben desde los hijos ============
function regar(id) {
  const p = plantas.value.find(x => x.id === id)
  if (!p) return
  p.diasSinRiego = 0
  notificar(`${p.nombre} regada 💧`)
}

function eliminar(id) {
  const p = plantas.value.find(x => x.id === id)
  if (!p) return
  plantas.value = plantas.value.filter(x => x.id !== id)
  destacadas.value = destacadas.value.filter(d => d !== id)
  notificar(`${p.nombre} eliminada del catálogo`)
}

function destacar(id) {
  destacadas.value = destacadas.value.includes(id)
    ? destacadas.value.filter(d => d !== id)
    : [...destacadas.value, id]
}

let temporizadorMensaje = null
function notificar(texto) {
  mensaje.value = texto
  clearTimeout(temporizadorMensaje)                       // limpieza también acá
  temporizadorMensaje = setTimeout(() => (mensaje.value = null), 2500)
}

// ============ WATCH: reaccionar a un dato concreto ============
const plantasCriticas = computed(() => plantas.value.filter(p => p.diasSinRiego >= 10))

watch(plantasCriticas, (nuevas) => {
  if (nuevas.length) console.warn('[ALERTA] Plantas críticas:', nuevas.map(p => p.nombre))
})
</script>

<template>
  <main class="app">
    <h1>🌱 Panel del Vivero El Brote</h1>

    <!-- Feedback al usuario: nunca una acción sin respuesta visible -->
    <p v-if="mensaje" class="toast" role="status">{{ mensaje }}</p>

    <!-- PanelBase con slot #titulo y slot #extra -->
    <PanelBase :tono="plantasCriticas.length ? 'alerta' : 'exito'">
      <template #titulo>
        <h2>
          {{ plantasCriticas.length ? '⚠️ Atención requerida' : '✅ Todo en orden' }}
        </h2>
      </template>

      <template #extra>
        <label class="escala">
          Texto: {{ escalaTexto }}px
          <input type="range" min="12" max="22" v-model.number="escalaTexto">
        </label>
      </template>

      <p v-if="plantasCriticas.length">
        {{ plantasCriticas.length }} planta(s) llevan 10+ días sin riego:
        <strong>{{ plantasCriticas.map(p => p.nombre).join(', ') }}</strong>
      </p>
      <p v-else>Ninguna planta en estado crítico. Buen trabajo.</p>
    </PanelBase>

    <!-- Pestañas + componente dinámico -->
    <nav class="tabs" role="tablist">
      <button
        v-for="tab in pestanas"
        :key="tab.clave"
        type="button"
        role="tab"
        :aria-selected="pestanaActiva === tab.clave"
        :class="{ activa: pestanaActiva === tab.clave }"
        @click="pestanaActiva = tab.clave"
      >
        {{ tab.etiqueta }}
      </button>
    </nav>

      <!-- Los props/listeners extra se reenvían al componente que toque.
           VistaEstadisticas simplemente ignora los que no declara. -->
    <KeepAlive>
      <component
        :is="componenteActivo"
        :plantas="plantas"
        :destacadas="destacadas"
        :escala-texto="escalaTexto"
        @regar="regar"
        @eliminar="eliminar"
        @destacar="destacar"
      />
    </KeepAlive>
  </main>
</template>

<style scoped>
.app { max-width:960px; margin:0 auto; padding:1.5rem; font-family:system-ui, sans-serif; }
h1 { font-size:1.5rem; margin:0 0 1.5rem; }

.toast {
  background:#0d2b1e; color:#c9f7e2; padding:0.75rem 1rem;
  border-radius:8px; margin:0 0 1rem; font-size:0.9rem;
}

.tabs { display:flex; gap:0.5rem; border-bottom:2px solid #e2e6ea; margin-bottom:1.5rem; }
.tabs button {
  min-height:44px; padding:0 1rem; border:none; background:none; cursor:pointer;
  font-weight:600; color:#6b7680; border-bottom:2px solid transparent; margin-bottom:-2px;
}
.tabs button.activa { color:#2f8f66; border-bottom-color:#42b883; }

.escala { display:flex; flex-direction:column; gap:0.25rem; font-size:0.75rem; color:#6b7680; }
</style>