<template>
  <VCard
    class="pa-5 d-flex justify-center flex-column"
    width="1200px"
    variant="outlined"
  >
    <VTextField
      v-model="search"
      label="Pesquisar por nome ou código"
      type="text"
      variant="outlined"
      clearable
    ></VTextField>
    <div class="d-flex align-center justify-center">
      <router-link
        class="d-flex justify-center px-1"
        :to="{ name: 'ByLetter', params: { letter } }"
        v-for="letter in letters"
        :key="letter"
        >{{ letter }}</router-link
      >
    </div>

    <VDataTable
      :headers="headers"
      :items="pratosFiltrados"
      :items-per-page="10"
      :page.sync="page"
      :items-length="totalItems"
      class="elevation-1 custom-background"
      loading-text="Loading... Please wait"
      :loading="loading"
    ></VDataTable>
    
    <div class="d-flex justify-end pa-4">
      <v-btn icon @click="prevPage" :disabled="page === 1">
        <v-icon>mdi-chevron-left</v-icon>
      </v-btn>
      <span>{{ page }} / {{ totalPages }}</span>
      <v-btn icon @click="nextPage" :disabled="page === totalPages">
        <v-icon>mdi-chevron-right</v-icon>
      </v-btn>
    </div>
  </VCard>
</template>


<script setup>
import { computed, onMounted, ref, watch } from "vue";
import { VCard, VTextField, VDataTable, VBtn, VIcon } from "vuetify/components";
import store from "../src/store";
import axiosClient from "../src/service/axiosCliente";

const meals = computed(() => store.state.meals);
const letters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ".split("");
const pratos = ref([]);
const search = ref("");
const pratosFiltrados = ref([]);
const loading = ref(true);
const page = ref(1);
const itemsPerPage = ref(10);
const totalItems = ref(0);

const headers = [
  { text: "ID", value: "idIngredient" },
  { text: "Ingredient", value: "strIngredient" },
  { text: "Description", value: "strDescription" },
  { text: "Type", value: "strType" },
];

const filtrarPratos = () => {
  const searchTerm = search.value.toLowerCase();
  if (!searchTerm) {
    pratosFiltrados.value = pratos.value;
  } else {
    pratosFiltrados.value = pratos.value.filter(
      (prato) =>
        prato.idIngredient.toLowerCase().includes(searchTerm) ||
        prato.strIngredient.toLowerCase().includes(searchTerm)
    );
  }
  totalItems.value = pratosFiltrados.value.length;
  pratosFiltrados.value = pratosFiltrados.value.slice(
    (page.value - 1) * itemsPerPage.value,
    page.value * itemsPerPage.value
  );
};

const prevPage = () => {
  if (page.value > 1) {
    page.value--;
    filtrarPratos();
  }
};

const nextPage = () => {
  if (page.value < totalPages.value) {
    page.value++;
    filtrarPratos();
  }
};

const totalPages = computed(() => Math.ceil(totalItems.value / itemsPerPage.value));

onMounted(async () => {
  try {
    const response = await axiosClient.get("/list.php?i=list");
    if (response.data.meals) {
      pratos.value = response.data.meals;
      filtrarPratos();
    } else {
      pratos.value = [];
    }
  } catch (error) {
    console.log(error);
  } finally {
    loading.value = false;
  }
});

watch(search, filtrarPratos);
watch(page, filtrarPratos);
</script>


<style>
.custom-background {
  background-color: #242424;
  color: #fff
}
</style>

