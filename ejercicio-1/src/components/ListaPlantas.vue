<script setup>
defineProps({
    items: { type: Array, default: () => [] }
})

</script>

<template>
    <ul v-if="items.length" class="lista">
        <li v-for="(item, indice) in items" :key="item.id">
            <!-- Exponemos datos AL PADRE a través del slot -->
            <slot name="item" :planta="item" :indice="indice">
                {{ item.nombre }}
            </slot>
        </li>
    </ul>

    <div v-else class="vacio">
        <p>Aún no hay plantas en el catálogo.</p>
        <slot name="empty-action" />
    </div>

</template>



<style scoped>
.lista {
    list-style: none;
    margin: 0;
    padding: 0;
    display: grid;
    gap: 0.5rem;
}

.vacio {
    text-align: center;
    padding: 2rem;
    color: #8b949e;
}
</style>