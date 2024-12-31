<script lang="ts">
import ModalContar from './modalContar.vue'
import Descripcion from './descripcion.vue'

export default {
    props: {
        showBillButton: {
            type: Boolean,
            default: false,
        },
    },
    components: {
        ModalContar,
        Descripcion
    },
    data() {
        return {
            showModal: false,
            inputValue: '',
            denominaciones: [
                { valor: 200.00, cantidad: 0 },
                { valor: 2.00, cantidad: 0 },
                { valor: 100.00, cantidad: 0 },
                { valor: 1.00, cantidad: 0 },
                { valor: 50.00, cantidad: 0 },
                { valor: 0.50, cantidad: 0 },
                { valor: 20.00, cantidad: 0 },
                { valor: 0.20, cantidad: 0 },
                { valor: 10.00, cantidad: 0 },
                { valor: 0.10, cantidad: 0 },
                { valor: 5.00, cantidad: 0 }
            ]
        }
    },
    methods: {
        clearInput() {
            this.inputValue = '';
            const input = this.$el.querySelector("input");
            if (input) input.focus();

            this.denominaciones = [
                { valor: 200.00, cantidad: 0 },
                { valor: 2.00, cantidad: 0 },
                { valor: 100.00, cantidad: 0 },
                { valor: 1.00, cantidad: 0 },
                { valor: 50.00, cantidad: 0 },
                { valor: 0.50, cantidad: 0 },
                { valor: 20.00, cantidad: 0 },
                { valor: 0.20, cantidad: 0 },
                { valor: 10.00, cantidad: 0 },
                { valor: 0.10, cantidad: 0 },
                { valor: 5.00, cantidad: 0 }
            ];

            this.$emit('updateSaldo', '0.00');
        },
        toggleModal() {
            this.showModal = !this.showModal;
        },
        handleConfirm(denominaciones: any) {
            this.denominaciones = denominaciones;
            const total = denominaciones.reduce((sum: number, den: any) => sum + (den.valor * den.cantidad), 0);
            this.inputValue = total.toFixed(2);
            this.showModal = false;

            this.$emit('updateSaldo', total.toFixed(2));
        },
        handleInput() {
            const total = parseFloat(this.inputValue || '0').toFixed(2);
            this.$emit('updateSaldo', total);
        },
        handleBlur() {
            let inputValueStr = this.inputValue.toString();
            if (inputValueStr === '' || !inputValueStr.includes('.')) {
                inputValueStr = (parseFloat(inputValueStr || '0').toFixed(2));
            } else {
                let [integerPart, decimalPart] = inputValueStr.split('.');
                if (decimalPart && decimalPart.length === 1) {
                    inputValueStr = `${integerPart}.${decimalPart}0`;
                }
            }
            this.inputValue = inputValueStr;
            this.$emit('updateSaldo', this.inputValue);
        }
    },
};
</script>

<template>
    <div class="relative">
        <div class="flex items-center gap-1">
            <div class="relative w-full">
                <input v-model="inputValue" type="number" @input="handleInput" @blur="handleBlur"
                    class="w-full p-2 pr-10 focus:outline-none border border-gray-300 rounded appearance-none text-black"
                    placeholder="S/. 0.00" />
                <i class="fas fa-times absolute right-3 top-1/2 transform -translate-y-1/2 text-gray-400 cursor-pointer"
                    @click="clearInput"></i>
            </div>
            <div v-if="showBillButton" class="bg-green-600 p-2 rounded mt-[0.49px] cursor-pointer" @click="toggleModal">
                <i class="fas fa-money-bill text-white text-xl mt-1"></i>
            </div>
        </div>

        <ModalContar v-if="showModal" @confirmar="handleConfirm" @cancelar="toggleModal"
            :initialDenominaciones="denominaciones" />

        <div v-if="showBillButton" class="mt-4">
            <span class="block font-semibold mb-2">Descripción</span>
            <Descripcion :denominaciones="denominaciones" />
        </div>
    </div>
</template>

<style scoped>
input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
    -webkit-appearance: none;
    margin: 0;
}

input[type="number"] {
    -moz-appearance: textfield;
}
</style>
