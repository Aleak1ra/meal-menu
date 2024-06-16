<template>
  <VCard class="pa-5 d-flex justify-center flex-column" width="1200px" variant="outlined">
    <VTextField
      v-model="search"
      label="Pesquisar um prato"
      type="text"
      variant="outlined"
      clearable
    ></VTextField>
    <div class="d-flex align-center justify-center">
      <router-link class="d-flex justify-center px-1" :to="{ name: 'ByLetter', params: { letter } }" v-for="letter in letters" :key="letter">{{ letter }}</router-link>
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
import { computed, onMounted, ref, watch } from "vue";
import { VCard, VTextField, VDataTable } from "vuetify/components";
import store from "../src/store";
import axiosClient from "../src/service/axiosCliente";

const meals = computed(() => store.state.meals);
const letters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ".split("");
const pratos = ref([]);
const search = ref("");
const filteredPratos = ref([]);

const headers = [
  { text: "ID", value: "idIngredient" },
  { text: "Ingredient", value: "strIngredient" },
  { text: "Description", value: "strDescription" },
  { text: "Type", value: "strType" },
];

const filterMeals = () => {
  const searchTerm = search.value.toLowerCase();
  if (!searchTerm) {
    filteredPratos.value = pratos.value;
  } else {
    filteredPratos.value = pratos.value.filter(prato =>
      prato.idIngredient.toLowerCase().includes(searchTerm) ||
      prato.strIngredient.toLowerCase().includes(searchTerm)
    );
  }
};

onMounted(async () => {
  try {
    const response = await axiosClient.get("/list.php?i=list");
    if (response.data.meals) {
      pratos.value = response.data.meals;
      filterMeals(); 
    } else {
      pratos.value = [];
    }
  } catch (error) {
    console.log(error);
  }
});


watch(search, () => {
  filterMeals();
});
</script>
