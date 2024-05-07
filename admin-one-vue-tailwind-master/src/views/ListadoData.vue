<script setup>
import { mdiChartTimelineVariant } from '@mdi/js'
</script>

<template>
  <LayoutAuthenticated>
    <SectionMain>
      <SectionTitleLineWithButton :icon="mdiChartTimelineVariant" title="Listado" main>
      </SectionTitleLineWithButton>

      <div class="flex items-center gap-3 mb-3 flex-wrap">
        <div class="">
          <select
            v-model="selectedPeriod"
            id="periodo"
            class="w-52 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
          >
            <option value="">Selecciona un periodo</option>
            <option v-for="(data, index) in periodos" :key="index" :value="data" >{{ data }}</option>
          </select>
        </div>

        <div class="">
          <select
            v-model="selectedFacultad"
            id="facultad"
            class="w-72 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
          >
            <option value="">Selecciona una facultad</option>
            <option v-for="(data, index) in facultades" :value="data"> {{ data }}</option>

          </select>
        </div>

        <div class="">
          <select
            v-model="selectedPrograma"
            id="programa"
            class="w-72 bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block p-2.5 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500"
          >
            <option value="">Selecciona un programa</option>
            <option v-for="(data, index) in programas" :value="data"> {{ data }}</option>
          </select>
        </div>

        <button
          type="button"
          @click="filterData"
          class="text-white bg-blue-700 hover:bg-blue-800 focus:ring-4 focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 dark:bg-blue-600 dark:hover:bg-blue-700 focus:outline-none dark:focus:ring-blue-800"
        >
          Filtrar
        </button>
        <button
          type="button"
          @click="cancelFilter"
          class="text-white bg-red-700 hover:bg-red-800 focus:ring-4 focus:ring-red-300 font-medium rounded-lg text-sm px-5 py-2.5 dark:bg-red-600 dark:hover:bg-red-700 focus:outline-none dark:focus:ring-red-800"
        >
          Cancelar
        </button>
      </div>

      <CardBox has-table>
        <table>
          <thead>
            <tr>
              <th rowspan="2" class="px-4 py-2 border">Periodo</th>
              <th rowspan="2" class="px-4 py-2 border">Facultad</th>
              <th rowspan="2" class="px-4 py-2 border">Programa</th>
              <th colspan="3" class="px-4 py-2 border text-center">Total matriculados</th>
              <th colspan="6" class="px-4 py-2 border text-center">Estrato</th>
            </tr>
            <tr>
              <th class="px-4 py-2 border text-center">Total</th>
              <th class="px-4 py-2 border text-center">Hombres</th>
              <th class="px-4 py-2 border text-center">Mujeres</th>
              <th class="px-4 py-2 border text-center">1</th>
              <th class="px-4 py-2 border text-center">2</th>
              <th class="px-4 py-2 border text-center">3</th>
              <th class="px-4 py-2 border text-center">4</th>
              <th class="px-4 py-2 border text-center">5</th>
              <th class="px-4 py-2 border text-center">6</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(data, index) in itemsPaginated" :key="index">
              <td data-label="Periodo">
                {{ data.periodo }}
              </td>
              <td data-label="Facultad">
                {{ data.facultad }}
              </td>
              <td data-label="Programa">
                {{ data.programa }}
              </td>
              <td data-label="Total matricula">
                {{ data.total_matricula }}
              </td>
              <td data-label="Total Masculino">
                {{ data.sexo_masc }}
              </td>
              <td data-label="Total Femenino">
                {{ data.sexo_feme }}
              </td>
              <td data-label="Estrato 1">
                {{ data.estrato_1 }}
              </td>
              <td data-label="Estrato 2">
                {{ data.estrato_2 }}
              </td>
              <td data-label="Estrato 3">
                {{ data.estrato_3 }}
              </td>
              <td data-label="Estrato 4">
                {{ data.estrato_4 }}
              </td>
              <td data-label="Estrato 5">
                {{ data.estrato_5 }}
              </td>
              <td data-label="Estrato 6">
                {{ data.estrato_6 }}
              </td>
            </tr>
            <tr v-if="itemsPaginated.length === 0">
              <td colspan="12" class="text-center text-gray-500">
                No se encontraron resultados
              </td>
            </tr>
          </tbody>
        </table>
        <div class="p-3 lg:px-6 border-t border-gray-100 dark:border-slate-800">
          <BaseLevel>
            <BaseButtons>
              <BaseButton
                v-for="page in pagesList"
                :key="page"
                :active="page === currentPage"
                :label="page + 1"
                :color="page === currentPage ? 'lightDark' : 'whiteDark'"
                small
                @click="currentPage = page"
              />
            </BaseButtons>
            <small>Page {{ currentPageHuman }} of {{ numPages }}</small>
          </BaseLevel>
        </div>
      </CardBox>
    </SectionMain>
  </LayoutAuthenticated>
</template>

<script>
//Se importan los componentes y bibliotecas
import SectionMain from '@/components/SectionMain.vue'
import CardBox from '@/components/CardBox.vue'
import LayoutAuthenticated from '@/layouts/LayoutAuthenticated.vue'
import SectionTitleLineWithButton from '@/components/SectionTitleLineWithButton.vue'
import CardBoxModal from '@/components/CardBoxModal.vue'
import BaseLevel from '@/components/BaseLevel.vue'
import BaseButtons from '@/components/BaseButtons.vue'
import BaseButton from '@/components/BaseButton.vue'
import axios from 'axios'

export default {
  component:{ //Componentes que se usan en la vista
    SectionMain,
    CardBox,
    LayoutAuthenticated,
    SectionTitleLineWithButton,
    CardBoxModal,
    BaseLevel,
    BaseButtons,
    BaseButton
  },
  mounted(){ //Este codigo se ejeuta una vez se monta el componente en la vista
      let data = []
      axios
        .get(`https://www.datos.gov.co/resource/r86y-229a.json`) //Se hace una peticion de todos los datos
        .then((result) => {
          data = result.data
          this.facultades = [...new Set(data.map(x => x.facultad))] //Se obtienen las facultades, Set no permite agregar elementos
          //duplicados en un arreglo
          this.programas = [...new Set(data.map(x => x.programa))] // Se obtienen los programas
          this.periodos = [...new Set(data.map(x => x.periodo))] // Se obtienen los periodos
          // Estos son los datos que se mostraran en el select para los filtros
        })
        .catch((error) => {
          alert(error.message)
        })

  },
  data(){
    return{ //Aqui se declaran las variables y constantes que se van a utilizar en el componente
      items: [],
      isModalActive: false,
      isModalDangerActive: false,
      perPage: 10,
      currentPage: 0,
      selectedFacultad: "",
      selectedPrograma: "",
      selectedPeriod: "",
      facultades: [],
      programas: [],
      periodos: []
    }
  },
  computed:{ //Estos metodos se ejecutan cada vez que cambia la variable items, para la cuestion del paginado de la tabla
    itemsPaginated(){  //Los datos que se van a mostrar en la vista
      return this.items.slice(this.perPage * this.currentPage, this.perPage * (this.currentPage + 1))
    },
    numPages(){ //Numero de botones en la pagina
      return Math.ceil(this.items.length / this.perPage)
    },
    currentPageHuman(){ //La pagina en la que se esta posicionado
      return this.currentPage + 1
    },
    pagesList(){  //Genera los botnes del paginado
        const pagesList = []

        for (let i = 0; i < this.numPages; i++) {
          pagesList.push(i)
        }

        return pagesList
    }
  },
  methods:{ //Metodos que se ejecutan para obtener la data
    filterData(){
      this.currentPage = 0

      //Se contruye la url para obtener los datos
      let url = `https://www.datos.gov.co/resource/r86y-229a.json`
      // Agregar el periodo a la URL si está definido
      if (this.selectedPeriod !== "") {
        url += `?periodo=${encodeURIComponent(this.selectedPeriod)}`;
      }

      // Agregar la facultad a la URL si está definida
      if (this.selectedFacultad !== "") {
        url += `${url.includes("?") ? "&" : "?"}facultad=${encodeURIComponent(this.selectedFacultad)}`;
      }

      // Agregar el programa a la URL si está definido
      if (this.selectedPrograma !== "") {
        url += `${url.includes("?") || url.includes("&") ? "&" : "?"}programa=${encodeURIComponent(this.selectedPrograma)}`;
      }
      axios
        .get(url)
        .then((result) => {
          this.items = result.data
        })
        .catch((error) => {
          alert(error.message)
        })
    },
    cancelFilter(){
      this.currentPage = 0
      this.selectedFacultad = ""
      this.selectedPeriod = ""
      this.selectedPrograma = ""
      this.items = []
    }
  }
}
</script>
