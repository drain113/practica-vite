<script setup lang="ts">
// import { defineProps } from 'vue';
import { Country } from '../models/country.model';

interface Props {
    country: Country
}

defineProps<Props>();
</script>

<template>
    <div class="h-56 sm:h-36">
        <img class="w-full h-56 sm:h-36" :src="country.flags.png" :alt="country.flags.alt">
    </div>

    <div class="text-xl font-bold">
        <h4>{{ country.name.common }}</h4>
    </div>

    <div class="text-sm text-gray-400">
        <p>Capital:
            <span v-for="capital in country.capital" class="font-bold text-red-400">{{ capital }}
            <template>, </template>
            </span>
        </p>
        <p>Región:
            <span class="font-bold text-sky-400">{{ country.region }}</span>
        </p>
        <p>Populación: 
            <span class="font-bold">{{ country.population?.toLocaleString() }}</span>
        </p>
    </div>
</template>