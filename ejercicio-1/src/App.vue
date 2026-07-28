<script setup>
import { computed, ref } from 'vue'
import TarjetaPlanta from './components/TarjetaPlanta.vue'
import PanelVivero from './components/PanelVivero.vue';
import ListaPlantas from './components/ListaPlantas.vue';

const plantas = ref([
  { id: 1, nombre: 'Monstera Deliciosa', precio: 12990, luz: 'Indirecta', stock: 3 },
  { id: 2, nombre: 'Sansevieria', precio: 8500, luz: 'Baja', stock: 0 },
  { id: 3, nombre: 'Potus', precio: 5990, luz: 'Indirecta', stock: 12 },
  { id: 4, nombre: 'Cactus San Pedro', precio: 15900, luz: 'Directa', stock: 5 }
]);

const carrito = ref([])
const total = computed(() => carrito.value.reduce((suma, item) => suma + item.precio, 0));
const totalFormateado = computed(() =>
  new Intl.NumberFormat('es-CL', {
    style: 'currency',
    currency: 'CLP'
  }).format(total.value)
);
function manejarAgregar(item) {
  carrito.value.push(item);
  const planta = plantas.value.find(p => p.id === item.id)
  if (planta) planta.stock--;
}
function manejarDetalle(id) {
  const planta = plantas.value.find(p => p.id === id);
  alert(`Detalle de: ${planta.nombre}`);
}

</script>

<template>
  <main class="catalogo">
    <header class="cabecera">
      <h1>Vivero El Brote</h1>
      <p>Carrito: {{ carrito.length }} · Total: {{ totalFormateado }}</p>
    </header>

    <PanelVivero variante="exito">
      <template #titulo>
        <h2>🌿 Cuidados de la semana</h2>
      </template>

      <p>Riega las suculentas cada 15 días, no cada 2. Menos es más.</p>
      <ul>
        <li>Revisar humedad del sustrato con el dedo</li>
        <li>Rotar macetas para luz pareja</li>
      </ul>

      <template #acciones>
        <button type="button">Marcar como leído</button>
      </template>
    </PanelVivero>

    <PanelVivero />
    <PanelVivero variante="alerta">
      <template #titulo>
        <h2>⚠️ Stock crítico</h2>
      </template>
      <p>Quedan 2 unidades de Sansevieria.</p>
    </PanelVivero>
    <section class="grid">

      <TarjetaPlanta v-for="planta in plantas" :id="planta.id" :key="planta.id" :nombre="planta.nombre"
        :precio="planta.precio" :luz="planta.luz" :stock="planta.stock" @agregar-carrito="manejarAgregar"
        @ver-detalle="manejarDetalle" />
    </section>





  
  
   <ListaPlantas :items="plantas">
    <template #item="{ planta, indice }">
      <strong>{{ indice + 1 }}.</strong>
      {{ planta.nombre }} — {{ planta.stock }} en stock
    </template>
    <template #empty-action>
      <button type="button">Cargar catálogo demo</button>
    </template>
  </ListaPlantas>
  </main>

 
</template>

<style>
/* --- Contenedor Principal --- */
.catalogo {
  max-width: 960px;
  margin: 0 auto;
  padding: 2rem 1.5rem;
  font-family: system-ui, -apple-system, sans-serif;
  color: #1a1a1a;
}

.cabecera {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 1rem;
  padding-bottom: 1.5rem;
  border-bottom: 1px solid #eaeaea;
  margin-bottom: 2rem;
}

.cabecera h1 {
  margin: 0;
  font-size: 2.2rem;
}

/* --- Grilla --- */
.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
  gap: 1.5rem;
}

/* --- Tarjetas de Producto --- */
.producto-card {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  /* Empuja el botón hacia abajo */
  position: relative;
  border: 1px solid #e5e7eb;
  border-radius: 12px;
  padding: 1.5rem;
  background-color: #ffffff;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.04);
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  min-height: 220px;
}

.producto-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 10px 15px rgba(0, 0, 0, 0.08);
}

/* --- Tipografía interna --- */
.producto-titulo {
  font-size: 1.25rem;
  font-weight: 700;
  margin: 0 0 0.75rem 0;
}

.producto-precio {
  font-size: 1.5rem;
  color: #2F855A;
  /* Verde oscuro estilo botánico */
  font-weight: bold;
  margin: 0 0 0.5rem 0;
}

.producto-luz {
  font-size: 0.9rem;
  color: #6b7280;
  margin: 0 0 1.5rem 0;
}

/* --- Etiquetas (Tags) --- */
.tag-agotada {
  position: absolute;
  top: 1rem;
  right: 1rem;
  color: #dc2626;
  /* Rojo para alertas */
  font-size: 0.85rem;
  font-weight: 600;
}

/* --- Botones --- */
.btn {
  width: 100%;
  padding: 0.85rem;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.2s;
}

.btn-agregar {
  background-color: #48BB78;
  /* Verde menta de la imagen */
  color: #064E3B;
  /* Texto oscuro para contraste */
}

.btn-agregar:hover {
  background-color: #38A169;
}

.btn-disabled {
  background-color: #cbd5e1;
  /* Gris de la imagen */
  color: #64748b;
  cursor: not-allowed;
}
</style>