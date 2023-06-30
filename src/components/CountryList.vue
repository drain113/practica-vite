<script setup lang="ts">
// import { defineProps } from 'vue';
import { Country } from '../models/country.model';
import CountryItem from "./CountryItem.vue"

interface Props {
    countries: Country[];
}

defineProps<Props>();
</script>

<template>
    <div class="grid grid-cols-1 gap-3 mt-8 sm:grid-cols-2 md:grid-cols-3">
        <div v-for="country in countries" class="p-3 border border-gray-300 rounded"><CountryItem :country="country"/></div>
    </div>
</template>
