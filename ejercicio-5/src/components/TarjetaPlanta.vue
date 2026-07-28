<script setup>
import { computed } from 'vue'

const props = defineProps({
  planta:      { type: Object, required: true },
  destacada:   { type: Boolean, default: false },
  escalaTexto: { type: Number,  default: 16 }
})

const emit = defineEmits(['regar', 'eliminar', 'destacar'])

const urgencia = computed(() => {
  const d = props.planta.diasSinRiego
  if (d >= 10) return 'critico'
  if (d >= 6)  return 'atencion'
  return 'ok'
})

const precioFormateado = computed(() =>
  new Intl.NumberFormat('es-CL', { style:'currency', currency:'CLP' })
    .format(props.planta.precio)
)

// CLASS BINDING con computed: la lógica vive en el script, no en el template
const clases = computed(() => [
  'tarjeta',
  `tarjeta--${urgencia.value}`,
  { 'tarjeta--destacada': props.destacada }
])

// STYLE BINDING
const estilos = computed(() => ({ fontSize: `${props.escalaTexto}px` }))
</script>

<template>
  <article :class="clases" :style="estilos">
    <header class="tarjeta__head">
      <h3>{{ planta.nombre }}</h3>
      <button
        type="button"
        class="icono"
        :aria-label="destacada ? 'Quitar destacado' : 'Destacar planta'"
        @click="emit('destacar', planta.id)"
      >
        {{ destacada ? '★' : '☆' }}
      </button>
    </header>

    <p class="precio">{{ precioFormateado }}</p>
    <p class="dato">Luz: {{ planta.luz }} · Stock: {{ planta.stock }}</p>
    <p class="dato">Sin riego hace <strong>{{ planta.diasSinRiego }}</strong> días</p>

    <footer class="acciones">
      <button type="button" class="btn btn--primario" @click="emit('regar', planta.id)">
        💧 Regar
      </button>
      <button type="button" class="btn btn--peligro" @click="emit('eliminar', planta.id)">
        Eliminar
      </button>
    </footer>
  </article>
</template>

<style scoped>
.tarjeta {
  border:1px solid #e2e6ea; border-left-width:4px; border-radius:12px;
  padding:1rem; background:#fff; transition:transform 0.15s ease, box-shadow 0.15s ease;
}
.tarjeta:hover { transform:translateY(-2px); box-shadow:0 4px 12px rgba(0,0,0,0.08); }

.tarjeta--ok       { border-left-color:#42b883; }
.tarjeta--atencion { border-left-color:#e8a33d; }
.tarjeta--critico  { border-left-color:#c0392b; background:#fdf3f2; }
.tarjeta--destacada{ box-shadow:0 0 0 2px #42b883; }

.tarjeta__head { display:flex; justify-content:space-between; align-items:flex-start; gap:0.5rem; }
.tarjeta__head h3 { margin:0; font-size:1.05em; }

.precio { font-weight:700; color:#2f8f66; margin:0.5rem 0; font-size:1.2em; }
.dato { color:#6b7680; font-size:0.85em; margin:0 0 0.25rem; }

.icono {
  min-width:44px; min-height:44px; border:none; background:none;
  font-size:1.25rem; cursor:pointer; color:#e8a33d;
}
.acciones { display:flex; gap:0.5rem; margin-top:1rem; }
.btn { flex:1; min-height:44px; border:none; border-radius:8px; cursor:pointer; font-weight:600; font-size:0.85em; }
.btn--primario { background:#42b883; color:#0d2b1e; }
.btn--peligro  { background:#fdecea; color:#c0392b; }
</style>