<script setup lang="ts">

import { onMounted, ref, watch } from "vue";
import PageHeader from './components/PageHeader.vue';
import axiosClient from './utils/axios';
import { Country } from './models/country.model';
import CountryList from './components/CountryList.vue';


const countries = ref<Country[]>([]);
const search = ref ("");
const filteredCountries = ref<Country[]>([]);


const fetchCountries = async () => {
  try {
    const { data } = await axiosClient.get("/all");
    countries.value = data;
    totalItems.value = countries.value.length;
  } catch (error) {
    console.log(error);
  }
};

const filterCountries = () => {
  if (search.value !== "" && page.value > 1) {
    page.value = 1;
  }
  
  filteredCountries.value = countries.value.filter((country) =>
    country.name.common.toLowerCase().includes(search.value.toLowerCase())
  );
};


const page = ref(1);
const itemPerPage= ref(18);
const paginatedCountries = ref<Country[]>([]);
const totalItems = ref (0);

const sliceCountries = (currentCountries: Country[]) => {
  const start = (page.value - 1) * itemPerPage.value;
  const end = page.value * itemPerPage.value;

  paginatedCountries.value = currentCountries.slice(start, end);

  if (paginatedCountries.value.length === 0 && page.value > 1) {
    page.value = 1;
  }
};


const changePage = (newPage: number) => {
  const totalPages = Math.ceil(
//  Si el valor filteredCountries.value.length es mayor a 0 (hay items filtrados), se dividen por los items en la página, del contrario, se dividirán los países en total.                                                                               
    filteredCountries.value.length > 0 ? filteredCountries.value.length / itemPerPage.value : countries.value.length / itemPerPage.value 
  );

  if (newPage >= 1 && newPage <= totalPages) {
    page.value = newPage;
  }
};

onMounted(() => {
  fetchCountries();
  sliceCountries(countries.value);
});

watch([countries, page, filteredCountries], () => {
  sliceCountries(
    filteredCountries.value.length <= 0 && search.value === "" ? countries.value : filteredCountries.value)
});

</script>

<template>
  <PageHeader />

  <div class="container max-w-screen-lg px-6 mx-auto ">
    <div class="mb-8">
      <input type="text" class="grid w-full grid-cols-1 gap-3 p-3 mt-8 border border-gray-300 rounded sm:grid-cols-2 md:grid-cols-3"
      placeholder="Buscar por nombre (en inglés)"
      v-model="search"
      @input="filterCountries">
    </div>
  
  <div class="flex justify-center mb-8 space-x-2">
    <button
    :disabled="page <= 1"
    :class="{ 'opacity-50': page <= 1 }" 
    @click="$event => changePage(page - 1)"

    class="px-8 py-0.5 mb-8 border border-gray-400 hover:bg-slate-200">Anterior
    </button>

    <button
    :disabled="page >= (totalItems / itemPerPage) "
    :class="{ 'opacity-50': page >= (totalItems / itemPerPage) || (filteredCountries.length > 0 && page >= (filteredCountries.length / itemPerPage)) }"
    @click="$event => changePage(page + 1)"
    class="px-8 py-0.5 mb-8 border border-gray-400 hover:bg-slate-200">Siguiente
    </button>
  </div>

    <CountryList :countries="paginatedCountries" />
  </div>

</template>

