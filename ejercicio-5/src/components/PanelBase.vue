<script setup>
defineProps({
  tono: {
    type: String,
    default: 'neutro',
    validator: (v) => ['neutro', 'exito', 'alerta'].includes(v)
  }
})
</script>

<template>
  <section class="panel" :class="`panel--${tono}`">
    <header class="panel__head">
      <slot name="titulo"><h2>Panel</h2></slot>
      <div v-if="$slots.extra" class="panel__extra">
        <slot name="extra" />
      </div>
    </header>

    <div class="panel__body">
      <slot><p class="vacio">Sin contenido.</p></slot>
    </div>
  </section>
</template>

<style scoped>
.panel { border:1px solid #e2e6ea; border-radius:12px; background:#fff; overflow:hidden; margin-bottom:1.5rem; }
.panel__head {
  display:flex; justify-content:space-between; align-items:center; gap:1rem;
  padding:1rem; border-bottom:1px solid #e2e6ea; background:#f6f7f9;
}
.panel__head :deep(h2) { margin:0; font-size:1.05rem; }
.panel__body { padding:1.5rem; }
.vacio { color:#8b949e; margin:0; }
.panel--exito  { border-color:#42b883; }
.panel--alerta { border-color:#e8a33d; }
</style>
