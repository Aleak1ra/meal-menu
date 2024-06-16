<template>
    <VCard class="pa-5 d-flex justify-center flex-column" width="1200px" variant="outlined">
      <h1>Meals starting with "{{ letter }}"</h1>
      <VDataTable
        :headers="headers"
        :items="pratos"
        :items-per-page="10"
        class="elevation-1"
      ></VDataTable>
    </VCard>
  </template>
  
  <script setup>
    import { computed, onMounted, ref } from "vue";
    import { VCard, VDataTable } from "vuetify/components";
    import axiosClient from "../src/service/axiosCliente";


  </script>
  