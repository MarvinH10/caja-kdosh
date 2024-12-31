<script lang="ts">
import { defineComponent } from 'vue';

interface Denominacion {
    valor: number;
    cantidad: number;
}

export default defineComponent({
    props: {
        denominaciones: {
            type: Array as () => Denominacion[],
            required: true,
        },
    },
    computed: {
        descriptionText(): string {
            return this.denominaciones
                .filter(den => den.cantidad > 0)
                .map(den => `- ${den.cantidad} x S/. ${den.valor.toFixed(2)}`)
                .join('\n');
        },
    },
});
</script>

<template>
    <div class="relative w-full">
        <textarea class="w-full p-2 focus:outline-none border border-gray-300 rounded text-black bg-white" rows="3"
            :value="descriptionText"></textarea>
    </div>
</template>
