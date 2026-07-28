<script setup>

import { ref, computed } from 'vue';

const diasSinRiego = ref(3);
const modoCompacto = ref(false);
const escala       = ref(16);
const colorMarca   = ref('#42b883');

const urgencia = computed(() => {
  if (diasSinRiego.value >= 10) return 'critico'
  if (diasSinRiego.value >= 6)  return 'atencion'
  return 'ok'
});

const clasesObjeto = computed(() => ({
  'etiqueta--compacta': modoCompacto.value,
  'etiqueta--critica':  urgencia.value === 'critico'
}));

const clasesArray = computed(() => [
  'etiqueta',
  `etiqueta--${urgencia.value}`,
  { 'etiqueta--compacta': modoCompacto.value }
]);

const estilosInline = computed(() => ({
  fontSize: `${escala.value}px`,
  padding: modoCompacto.value ? '0.5rem' : '1rem'
}));

</script>

<template>
  <div class="demo">

    <!-- 1. Objeto inline: lo más legible para 1-2 condiciones -->
    <p class="etiqueta"
      :class="{ 'etiqueta--critica': urgencia === 'critico' }">
      Sintaxis objeto inline
    </p>

    <!-- 2. Computed devolviendo objeto: para lógica más compleja -->
    <p class="etiqueta" :class="clasesObjeto">
      Sintaxis objeto vía computed
    </p>

    <!-- 3. Array: la clase estática y la dinámica se FUSIONAN, no se pisan -->
    <p :class="clasesArray" :style="estilosInline">
      Sin riego hace {{ diasSinRiego }} días — {{ urgencia }}
    </p>

    <!-- 4. Style binding directo -->
    <p :style="{ color: colorMarca, fontWeight: 700 }">
      Color controlado por variable JS
    </p>

    <hr>

    <div class="controles">
      <label>
        Días sin riego: {{ diasSinRiego }}
        <input type="range" min="0" max="14" v-model.number="diasSinRiego">
      </label>

      <label>
        Tamaño: {{ escala }}px
        <input type="range" min="12" max="32" v-model.number="escala">
      </label>

      <label>
        <input type="checkbox" v-model="modoCompacto">
        Modo compacto
      </label>

      <label>
        Color de marca
        <input type="color" v-model="colorMarca">
      </label>
    </div>
  </div>
</template>
<style scoped>
.demo { max-width:520px; }

.etiqueta {
  border-radius:8px;
  padding:1rem;
  margin:0 0 0.5rem;
  background:#f6f7f9;
  border-left:4px solid #d5dade;
  transition: all 0.2s ease;
}

.etiqueta--ok       { border-left-color:#42b883; background:#f0f9f5; }
.etiqueta--atencion { border-left-color:#e8a33d; background:#fdf6e8; }
.etiqueta--critico,
.etiqueta--critica  { border-left-color:#c0392b; background:#fdecea; color:#8e2b21; }
.etiqueta--compacta { padding:0.5rem; font-size:0.875rem; }

/* v-bind() en CSS: enlaza una variable del script DIRECTAMENTE al estilo.
   Vue la compila a una custom property de CSS. Vue 3.2+. */
hr { border:none; border-top:2px solid v-bind(colorMarca); margin:1.5rem 0; }

.controles { display:grid; gap:1rem; }
.controles label { display:flex; flex-direction:column; gap:0.25rem; font-size:0.875rem; color:#4a5560; }
</style>