<script lang="ts">
import { defineComponent } from 'vue';
import Input from './components/input.vue';
import * as XLSX from 'xlsx';

export default defineComponent({
  components: {
    Input,
  },
  data() {
    return {
      saldoInicial: '0.00',
      saldoBanco: '0.00',
      saldoYape: '0.00',
      saldoPagoEfectivo: '0.00',
    };
  },
  methods: {
    updateSaldo(newSaldo: string) {
      this.saldoInicial = newSaldo;
    },
    updateSaldoBanco(newSaldo: string) {
      this.saldoBanco = newSaldo;
    },
    updateSaldoYape(newSaldo: string) {
      this.saldoYape = newSaldo;
    },
    updateSaldoPagoEfectivo(newSaldo: string) {
      this.saldoPagoEfectivo = newSaldo;
    },
    downloadExcel() {
      const data = [
        ['𝐃𝐞𝐬𝐜𝐫𝐢𝐩𝐜𝐢ó𝐧', '𝐌𝐨𝐧𝐭𝐨'],
        ['Apertura', `S/. ${this.saldoInicial}`],
        ['Pagos en Efectivo', `S/. ${this.saldoPagoEfectivo}`],
        ['Total Efectivo', `S/. ${this.totalEfectivo}`],
        ['Banco Contado', `S/. ${this.saldoBanco}`],
        ['Yape Contado', `S/. ${this.saldoYape}`],
        ['Total', `S/. ${this.total}`],
      ];

      const ws = XLSX.utils.aoa_to_sheet(data);

      const style = {
        font: { bold: true },
        fill: { fgColor: { rgb: 'C6EFCE' } },
      };

      const totalRowIndexes = [3, 7];
      totalRowIndexes.forEach(index => {
        const cellA = `A${index + 2}`;
        const cellB = `B${index + 2}`;
        if (ws[cellA]) {
          ws[cellA].s = style;
        }
        if (ws[cellB]) {
          ws[cellB].s = style;
        }
      });

      const wb = XLSX.utils.book_new();
      XLSX.utils.book_append_sheet(wb, ws, 'Cuadre de Caja');

      XLSX.writeFile(wb, 'cuadre_de_caja.xlsx');
    },
  },
  computed: {
    totalEfectivo(): string {
      const total = parseFloat(this.saldoInicial) + parseFloat(this.saldoPagoEfectivo);
      return total.toFixed(2);
    },
    total(): string {
      const totalSaldo =
        parseFloat(this.saldoBanco) +
        parseFloat(this.saldoYape) +
        parseFloat(this.saldoPagoEfectivo) +
        parseFloat(this.saldoInicial);
      return totalSaldo.toFixed(2);
    },
  },
});
</script>

<template>
  <div class="min-h-screen bg-[#181818] text-white flex flex-col gap-4 md:gap-8 relative">
    <div class="relative flex items-center gap-4">
      <img src="/blancokdosh.png" alt="Kdosh Store" class="ml-4 w-[50px] h-360px] md:w-[80px] md:h-[60px]">
      <div class="text-xl md:text-2xl font-bold mt-4 md:mt-6 ml-1 md:ml-6 mb-4 md:mb-6">
        CONTROL DE CUADRE DE CAJA
      </div>
      <div class="absolute top-full left-0 w-full border-b border-gray-400"></div>
    </div>

    <div class="bg-white mx-4 md:mx-9 rounded-md shadow-md mt-8 md:mt-20">
      <div class="flex flex-col lg:flex-row gap-4 md:gap-6 p-4 md:p-8">
        <div class="bg-white text-[#181818] w-full lg:w-1/4 flex flex-col justify-center items-center">
          <div class="w-full max-w-sm">
            <span class="block font-semibold">Saldo Inicial</span>
            <Input :showBillButton="true" @updateSaldo="updateSaldo" />
          </div>
        </div>

        <div
          class="bg-[#181818] text-white p-4 md:p-6 rounded-md shadow-md flex flex-col lg:flex-row gap-6 xl:w-[1240px] w-full justify-center items-center">
          <div class="flex-1">
            <span class="block font-semibold mb-4">MÉTODOS DE PAGO</span>
            <div class="flex flex-col gap-4 md:gap-6">
              <div>
                <span class="block font-semibold mb-2">Banco</span>
                <Input :showBillButton="false" @updateSaldo="updateSaldoBanco" />
              </div>
              <div>
                <span class="block font-semibold mb-2">Yape</span>
                <Input :showBillButton="false" @updateSaldo="updateSaldoYape" />
              </div>
              <div>
                <span class="block font-semibold mb-2">Efectivo</span>
                <Input :showBillButton="true" @updateSaldo="updateSaldoPagoEfectivo" />
              </div>
            </div>
          </div>

          <div class="flex-1 lg:ml-6">
            <span class="block font-bold mb-2 text-sm">EFECTIVO CONTADO</span>
            <ul class="mb-2 text-sm">
              <div class="border-l border-gray-500">
                <li class="flex justify-between ml-5 mb-3">
                  <span><i class="fa-solid fa-plus mr-2"></i> Apertura</span>
                  <span>S/. {{ saldoInicial }}</span>
                </li>
                <li class="flex justify-between ml-5 mb-3">
                  <span><i class="fa-solid fa-plus mr-2"></i> Pagos en Efectivo</span>
                  <span>S/. {{ saldoPagoEfectivo }}</span>
                </li>
                <li class="flex justify-between ml-5 font-bold mb-3">
                  <span><i class="fa-solid fa-plus mr-2"></i> Total Efectivo</span>
                  <span
                    class="relative before:border-l-[50px] before:block before:absolute before:w-full before:h-[1px] before:bg-gray-500 before:top-0">
                    S/. {{ totalEfectivo }}
                  </span>
                </li>
              </div>
            </ul>

            <div class="flex justify-between font-bold text-sm mb-3">
              <span>BANCO CONTADO</span>
              <span>S/. {{ saldoBanco }}</span>
            </div>
            <div class="flex justify-between font-bold text-sm mb-3">
              <span>YAPE CONTADO</span>
              <span>S/. {{ saldoYape }}</span>
            </div>
            <div class="flex justify-between font-bold text-sm">
              <span class="ml-auto mr-4">TOTAL</span>
              <span
                class="relative before:border-l-[50px] before:block before:absolute before:w-full before:h-[1px] before:bg-gray-500 before:top-0">
                S/. {{ total }}
              </span>
            </div>

            <div class="flex justify-end mt-6 md:mt-10">
              <button @click="downloadExcel"
                class="bg-green-500 hover:bg-green-600 text-white py-2 px-4 rounded shadow flex items-center gap-2">
                <i class="fas fa-file-excel text-white"></i>
                Descargar
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
