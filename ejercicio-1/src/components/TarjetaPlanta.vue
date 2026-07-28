<script setup>
import { computed } from 'vue';


const props = defineProps({
    id:     { type: Number, required: true },
    nombre: { type: String, required: true },
    precio: { type: Number, required: true },
    luz: { type: String, default: 'Indirecta', validator: (valor) => ['Directa', 'Indirecta', 'Baja'].includes(valor) },
    stock: { type: Number, default: 0 },
});
const emit= defineEmits(['agregar-carrito','ver-detalle']);

const precioFormateado= computed(()=>
new Intl.NumberFormat('es-CL', {
    style: 'currency',
    currency: 'CLP'
  }).format(props.precio)
);

const hayStock= computed(()=> props.stock > 0);

function agregarAlCarrito() {
    emit('agregar-carrito', { id: props.id, nombre: props.nombre, precio: props.precio })
}


</script>
<template>
    <article class="producto-card">
        <div class="info-principal">
            <h3  @click="emit('ver-detalle',id)" >{{ nombre }}</h3>
            <p class="precio">{{ precioFormateado }}</p>
            <p class="luz">{{ luz }}</p>

        </div>
        <p v-if="hayStock" class="stock-ok"> {{ stock }} </p>
        <p v-else  class="stock-no">Agotada </p>
    
         <button
          type="button"
          :disabled="!hayStock"
          @click="agregarAlCarrito"
        >
          Agregar al carrito
        </button>
    </article>

</template>



<style scoped>
.tarjeta {
  border: 1px solid #e2e6ea;
  border-radius: 12px;
  padding: 1rem;
  background: #fff;
}
.precio { font-weight: 700; color: #2f8f66; margin: 0.5rem 0; font-size: 1.25rem; }
.luz { color: #6b7680; font-size: 0.875rem; margin: 0 0 0.5rem; }
.stock-ok { color: #2f8f66; font-size: 0.875rem; margin: 0; }
.stock-no { color: #c0392b; font-size: 0.875rem; margin: 0; }
button {
  width:100%; min-height:44px; border:none; border-radius:8px;
  background:#42b883; color:#0d2b1e; font-weight:700; cursor:pointer;
}
button:disabled { background:#d5dade; color:#8b949e; cursor:not-allowed; }
</style>