<script setup>
import { mdiTrendingUp } from '@mdi/js';
</script>

<template>
  <LayoutAuthenticated>
    <SectionMain>
      <SectionTitleLineWithButton :icon="mdiTrendingUp" title="Listado" main>
      </SectionTitleLineWithButton>

      <div class="flex items-center gap-3 mb-3 flex-wrap">

        <div class="">
          <select v-model="selectedPeriod" id="periodo"
            class="w-52 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500">
            <option v-for="(data, index) in periodos" :key="index" :value="data" >{{ data }}</option>
          </select>
        </div>

        <div class="">
          <select v-model="selectedGraph" id="facultad"
            class="w-72 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500">
            <option value="todos">Total matriculados</option>
            <option value="hombres">Total de hombres</option>
            <option value="mujeres">Total de mujeres</option>
          </select>
        </div>

        <button type="button" @click="filterData"
          class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5  dark:bg-blue-600 dark:hover:bg-blue-700 focus:outline-none dark:focus:ring-blue-800">Graficar</button>
        <button type="button" @click="cancelFilter"
          class="text-white bg-red-700 hover:bg-red-800 focus:ring-4 focus:ring-red-300 font-medium rounded-lg text-sm px-5 py-2.5  dark:bg-red-600 dark:hover:bg-red-700 focus:outline-none dark:focus:ring-red-800">Cancelar</button>
      </div>

      <CardBox has-table>
        <div>
          <!-- Mostrar la grafica -->
          <canvas id="myChart"></canvas>
        </div>
      </CardBox>

    </SectionMain>
  </LayoutAuthenticated>
</template>

<script>
import SectionMain from '@/components/SectionMain.vue';
import CardBox from '@/components/CardBox.vue';
import LayoutAuthenticated from '@/layouts/LayoutAuthenticated.vue';
import SectionTitleLineWithButton from '@/components/SectionTitleLineWithButton.vue';
import axios from 'axios';
import Chart from 'chart.js/auto';

export default {
  components: {
    SectionMain,
    CardBox,
    LayoutAuthenticated,
    SectionTitleLineWithButton
  },
  mounted() {
    let data = []
      axios
        .get(`https://www.datos.gov.co/resource/r86y-229a.json`)
        .then((result) => {
          data = result.data
          this.periodos = [...new Set(data.map(x => x.periodo))]
        })
        .catch((error) => {
          alert(error.message)
        })
  },
  data() {
    return {
      items: [],
      selectedPeriod: "2015-1",
      selectedGraph: "todos",
      myChart: null,
      periodos: []
    };
  },
  methods: {
    filterData() {
      let url = `https://www.datos.gov.co/resource/r86y-229a.json`;
      if (this.selectedPeriod !== "") {
        url += `?periodo=${encodeURIComponent(this.selectedPeriod)}`;
      }

      axios
        .get(url)
        .then((result) => {
          this.items = result.data;
          this.renderChart();
        })
        .catch((error) => {
          alert(error.message);
        });
    },
    renderChart() {
      if (this.myChart) { // Verifica si hay una grafica, si existe la elimna
        this.destroyChart();
      }

      const ctx = document.getElementById('myChart').getContext('2d'); //Seleciona donde se va a mostrar la grafica
      const filteredData = this.filterChartData(); //Se obtienen los datos de la grafica

      this.myChart = new Chart(ctx, {
        type: 'bar', // tipo de grafica
        data: {
          labels: filteredData.map(item => item.programa), // se filtran los nombres de las carreas
          datasets: [{
            label: this.selectedGraph === 'todos' ? 'Total Matriculados' : `Total ${this.selectedGraph}`, //titulo 
            data: filteredData.map(item => {
              if (this.selectedGraph === 'todos') {
                return item.total_matricula;
              } else if (this.selectedGraph === 'hombres') {
                return item.sexo_masc;
              } else if (this.selectedGraph === 'mujeres') {
                return item.sexo_feme;
              }
            }),
            backgroundColor: 'rgba(54, 162, 235, 0.2)',
            borderColor: 'rgba(54, 162, 235, 1)',
            borderWidth: 1
          }]
        },
        options: {
          scales: {
            y: {
              beginAtZero: true
            }
          }
        }
      });
    },
    destroyChart() {
      if (this.myChart != null) {
        this.myChart.destroy();
        this.myChart = null;
      }
    },
    cancelFilter() {
      this.selectedGraph = "todos";
      this.selectedPeriod = "2015-1";
      this.items = [];
      this.destroyChart();
    },
    filterChartData() {
      if (this.selectedGraph === 'todos') {
        return this.items;
      } else if (this.selectedGraph === 'hombres') {
        return this.items.filter(item => item.sexo_masc !== "");
      } else if (this.selectedGraph === 'mujeres') {
        return this.items.filter(item => item.sexo_feme !== "");
      }
    }
  }
};
</script>
