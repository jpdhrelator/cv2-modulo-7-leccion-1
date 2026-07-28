<script setup>
import { ref, computed, onMounted, onUnmounted, onActivated, onDeactivated } from 'vue'

const props = defineProps({
  plantas: { type: Array, default: () => [] }
})

const cargando   = ref(true)
const segundos   = ref(0)
const temperatura = ref(21)
let cronometro = null
let sensor = null

const totalStock = computed(() => props.plantas.reduce((s, p) => s + p.stock, 0))
const valorTotal = computed(() =>
  new Intl.NumberFormat('es-CL', { style:'currency', currency:'CLP' })
    .format(props.plantas.reduce((s, p) => s + p.precio * p.stock, 0))
)
const conSed = computed(() => props.plantas.filter(p => p.diasSinRiego >= 6).length)

onMounted(async () => {
  console.log('[Estadísticas] onMounted')
  await new Promise(r => setTimeout(r, 700))   // simula carga de API
  cargando.value = false

  cronometro = setInterval(() => segundos.value++, 1000)
  sensor     = setInterval(() => {
    temperatura.value = +(18 + Math.random() * 10).toFixed(1)
  }, 3000)
})

// Con KeepAlive el componente NO se desmonta al cambiar de pestaña:
// se desactiva. Por eso estos hooks existen.
onActivated(()   => console.log('[Estadísticas] onActivated — estado conservado'))
onDeactivated(() => console.log('[Estadísticas] onDeactivated — sigue en memoria'))

onUnmounted(() => {
  clearInterval(cronometro)
  clearInterval(sensor)
  console.log('[Estadísticas] onUnmounted — timers detenidos')
})
</script>

<template>
  <div>
    <div v-if="cargando" class="skeleton">
      <div class="linea"></div><div class="linea"></div><div class="linea corta"></div>
    </div>

    <div v-else class="stats">
      <div class="stat"><span class="stat__n">{{ plantas.length }}</span><span>especies</span></div>
      <div class="stat"><span class="stat__n">{{ totalStock }}</span><span>unidades</span></div>
      <div class="stat"><span class="stat__n">{{ valorTotal }}</span><span>inventario</span></div>
      <div class="stat" :class="{ 'stat--alerta': conSed > 0 }">
        <span class="stat__n">{{ conSed }}</span><span>con sed</span>
      </div>
      <div class="stat"><span class="stat__n">{{ temperatura }}°</span><span>invernadero</span></div>
      <div class="stat"><span class="stat__n">{{ segundos }}s</span><span>sesión</span></div>
    </div>
  </div>
</template>

<style scoped>
.stats { display:grid; grid-template-columns:repeat(auto-fit,minmax(140px,1fr)); gap:1rem; }
.stat {
  display:flex; flex-direction:column; gap:0.25rem;
  padding:1rem; border-radius:12px; background:#f6f7f9; border-left:4px solid #42b883;
}
.stat--alerta { border-left-color:#c0392b; background:#fdecea; }
.stat__n { font-size:1.5rem; font-weight:700; color:#1b1f24; }
.stat span:last-child { font-size:0.8rem; color:#6b7680; text-transform:uppercase; letter-spacing:0.05em; }
.skeleton .linea { height:2.5rem; background:#e2e6ea; border-radius:8px; margin-bottom:0.5rem; animation:pulso 1.5s infinite; }
.skeleton .corta { width:50%; }
@keyframes pulso { 0%,100%{opacity:1} 50%{opacity:0.5} }
</style>