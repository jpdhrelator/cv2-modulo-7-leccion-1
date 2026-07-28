<script setup>
    defineProps({
    variante: {
        type: String,
        default: 'neutro',
        validator: (v) => ['neutro', 'exito', 'alerta'].includes(v)
    }
    })
</script>


<template>
  <section class="panel" :class="`panel--${variante}`">

    <!-- SLOT CON NOMBRE: el padre lo llena con #titulo -->
    <header class="panel__head">
      <slot name="titulo">
        <!-- Contenido de FALLBACK: se muestra si el padre no envía nada -->
        <h2>Panel sin título</h2>
      </slot>
    </header>

    <!-- SLOT POR DEFECTO: todo lo que el padre ponga suelto cae acá -->
    <div class="panel__body">
      <slot>
        <p class="vacio">No hay contenido todavía.</p>
      </slot>
    </div>

    <!-- Renderizamos el footer SOLO si el padre lo envió.
         $slots te dice qué slots recibiste. -->
    <footer v-if="$slots.acciones" class="panel__foot">
      <slot name="acciones" />
    </footer>

  </section>
</template>

<style scoped>
.panel { border:1px solid #e2e6ea; border-radius:12px; overflow:hidden; background:#fff; }
.panel__head { padding:1rem; border-bottom:1px solid #e2e6ea; }
.panel__head :deep(h2) { margin:0; font-size:1.125rem; }
.panel__body { padding:1rem; }
.panel__foot { padding:1rem; border-top:1px solid #e2e6ea; background:#f6f7f9; display:flex; gap:0.5rem; }
.vacio { color:#8b949e; margin:0; }

.panel--exito  { border-color:#42b883; }
.panel--alerta { border-color:#e8a33d; }
</style>