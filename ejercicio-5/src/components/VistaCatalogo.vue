<script setup>
import { ref, computed } from 'vue'
import TarjetaPlanta from './TarjetaPlanta.vue'

const props = defineProps({
  plantas:     { type: Array,  default: () => [] },
  destacadas:  { type: Array,  default: () => [] },
  escalaTexto: { type: Number, default: 16 }
})

// Reemitimos hacia arriba: el catálogo tampoco es dueño de los datos.
const emit = defineEmits(['regar', 'eliminar', 'destacar'])

const filtro = ref('todas')

const plantasFiltradas = computed(() => {
  if (filtro.value === 'sed')     return props.plantas.filter(p => p.diasSinRiego >= 6)
  if (filtro.value === 'agotadas') return props.plantas.filter(p => p.stock === 0)
  return props.plantas
})
</script>

<template>
  <div>
    <nav class="filtros">
      <button
        v-for="f in ['todas','sed','agotadas']"
        :key="f"
        type="button"
        :class="{ activo: filtro === f }"
        @click="filtro = f"
      >
        {{ f }}
      </button>
    </nav>

    <!-- EMPTY STATE: obligatorio. Nunca una lista vacía sin mensaje. -->
    <div v-if="!plantasFiltradas.length" class="vacio">
      <p>🪴 No hay plantas que coincidan con "{{ filtro }}".</p>
      <button type="button" @click="filtro = 'todas'">Ver todas</button>
    </div>

    <section v-else class="grid">
      <TarjetaPlanta
        v-for="planta in plantasFiltradas"
        :key="planta.id"
        :planta="planta"
        :destacada="destacadas.includes(planta.id)"
        :escala-texto="escalaTexto"
        @regar="emit('regar', $event)"
        @eliminar="emit('eliminar', $event)"
        @destacar="emit('destacar', $event)"
      />
    </section>
  </div>
</template>

<style scoped>
.filtros { display:flex; gap:0.5rem; margin-bottom:1.5rem; }
.filtros button {
  min-height:44px; padding:0 1rem; border:1px solid #e2e6ea; border-radius:8px;
  background:#fff; cursor:pointer; text-transform:capitalize; font-weight:600; color:#6b7680;
}
.filtros button.activo { background:#42b883; border-color:#42b883; color:#0d2b1e; }
.grid { display:grid; grid-template-columns:repeat(auto-fill,minmax(240px,1fr)); gap:1rem; }
.vacio { text-align:center; padding:3rem 1rem; color:#8b949e; }
.vacio button { min-height:44px; padding:0 1.5rem; margin-top:1rem; border:none; border-radius:8px; background:#42b883; color:#0d2b1e; font-weight:700; cursor:pointer; }
</style>
