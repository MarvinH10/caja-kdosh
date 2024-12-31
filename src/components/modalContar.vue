<script lang="ts">
import { ref, watch, onMounted } from 'vue'

interface Denominacion {
    valor: number;
    cantidad: number;
}

export default {
    props: {
        initialDenominaciones: {
            type: Array as () => Denominacion[],
            required: true
        }
    },
    emits: ['confirmar', 'cancelar'],
    setup(props, { emit }) {
        const denominaciones = ref<Denominacion[]>([]);
        const total = ref(0);

        watch(denominaciones, () => calcularTotal(), { deep: true });

        onMounted(() => {
            denominaciones.value = [...props.initialDenominaciones];
        });

        const incrementar = (index: number) => {
            denominaciones.value[index].cantidad++;
        };

        const decrementar = (index: number) => {
            if (denominaciones.value[index].cantidad > 0) {
                denominaciones.value[index].cantidad--;
            }
        };

        const calcularTotal = () => {
            total.value = denominaciones.value.reduce((sum, den) => sum + (den.valor * den.cantidad), 0);
        };

        const confirmar = () => {
            emit('confirmar', denominaciones.value);
        };

        const cancelar = () => {
            emit('cancelar');
        };

        return {
            denominaciones,
            total,
            incrementar,
            decrementar,
            confirmar,
            cancelar
        };
    }
};
</script>

<template>
    <div class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
        <div class="bg-[#181818] p-6 rounded-lg shadow-xl w-full max-w-md">
            <h2 class="text-white text-xl font-bold mb-4">MONEDAS/BILLETES</h2>

            <div class="grid grid-cols-2 gap-3">
                <div v-for="(den, index) in denominaciones.slice(0, 5)" :key="`col1-${index}`"
                    class="flex items-center justify-between rounded p-2">
                    <div class="bg-[#1E1E1E] flex items-center rounded">
                        <button @click="decrementar(index)" :disabled="den.cantidad <= 0"
                            class="w-8 h-8 flex items-center justify-center text-white bg-gray-500 hover:bg-gray-600 rounded disabled:opacity-50 disabled:hover:bg-gray-500 disabled:cursor-not-allowed">
                            <i class="fas fa-minus"></i>
                        </button>

                        <div class="flex items-center">
                            <span class="text-white mx-3">{{ den.cantidad }}</span>
                        </div>

                        <button @click="incrementar(index)"
                            class="w-8 h-8 flex items-center justify-center text-white bg-gray-500 hover:bg-gray-600 rounded">
                            <i class="fas fa-plus"></i>
                        </button>
                    </div>
                    <span class="text-white lg:text-sm text-[9.4px] flex-1 text-left ml-1">S/. {{ den.valor.toFixed(2) }}</span>
                </div>

                <div v-for="(den, index) in denominaciones.slice(5, 11)" :key="`col2-${index}`"
                    class="flex items-center justify-between rounded p-2">
                    <div class="bg-[#1E1E1E] flex items-center rounded">
                        <button @click="decrementar(index + 5)" :disabled="den.cantidad <= 0"
                            class="w-8 h-8 flex items-center justify-center text-white bg-gray-500 hover:bg-gray-600 rounded disabled:opacity-50 disabled:hover:bg-gray-500 disabled:cursor-not-allowed">
                            <i class="fas fa-minus"></i>
                        </button>

                        <div class="flex items-center">
                            <span class="text-white mx-3">{{ den.cantidad }}</span>
                        </div>

                        <button @click="incrementar(index + 5)"
                            class="w-8 h-8 flex items-center justify-center text-white bg-gray-500 hover:bg-gray-600 rounded">
                            <i class="fas fa-plus"></i>
                        </button>
                    </div>
                    <span class="text-white lg:text-sm text-[9.4px] flex-1 text-left ml-1">S/. {{ den.valor.toFixed(2) }}</span>
                </div>
            </div>

            <div class="mt-4 border-t border-gray-700 pt-4">
                <div class="text-white text-right mb-4">
                    TOTAL: S/. {{ total.toFixed(2) }}
                </div>

                <div class="flex justify-between gap-4">
                    <button @click="confirmar"
                        class="flex-1 bg-green-600 text-white py-2 px-4 rounded hover:bg-green-700 transition-colors">
                        CONFIRMAR
                    </button>
                    <button @click="cancelar"
                        class="flex-1 bg-red-600 text-white py-2 px-4 rounded hover:bg-red-700 transition-colors">
                        DESCARTAR
                    </button>
                </div>
            </div>
        </div>
    </div>
</template>
