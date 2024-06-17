<template>
  <VCard class="pa-5 d-flex justify-center flex-column" width="1200px" variant="outlined">
    <div class="d-flex align-center justify-center">
      <router-link class="d-flex justify-center px-1" :to="{ name: 'Home' }">Voltar para home</router-link>
    </div>
    <VDataTable
      :headers="headers"
      :items="filteredPratos"
      :items-per-page="10"
      class="elevation-1"
    ></VDataTable>
  </VCard>
</template>

<script setup>
import { onMounted, ref, watch } from 'vue';
import { useRoute } from 'vue-router';
import axiosClient from '../src/service/axiosCliente';

const route = useRoute();
const pratos = ref([]);
const filteredPratos = ref([]);
const headers = [
  { text: "ID", value: "idIngredient" },
  { text: "Ingredient", value: "strIngredient" },
  { text: "Description", value: "strDescription" },
  { text: "Type", value: "strType" },
];

const filterMeals = (letter) => {
  filteredPratos.value = pratos.value.filter(prato =>
    prato.strIngredient.toLowerCase().startsWith(letter.toLowerCase())
  );
};

const fetchMeals = async () => {
  try {
    const response = await axiosClient.get('/list.php?i=list');
    if (response.data.meals) {
      pratos.value = response.data.meals;
      filterMeals(route.params.letter);
    } else {
      pratos.value = [];
    }
  } catch (error) {
    console.log(error);
  }
};

onMounted(() => {
  fetchMeals();
});

watch(() => route.params.letter, (newLetter) => {
  filterMeals(newLetter);
});
</script>
