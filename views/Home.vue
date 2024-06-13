<template>
  <VCard class="pa-5 d-flex justify-center flex-column" width="1200px" variant="outlined">
    <VTextField
      label="Search meals"
      type="text"
      variant="outlined"
      clearable
    ></VTextField>
    <div class="d-flex align-center justify-center">
      <router-link class="d-flex justify-center px-1" :to="{name: 'ByLetter', params: {letter}}" v-for="letter of letters" :key="letter">{{ letter }}</router-link>
    </div>
  </VCard>
</template>

<script setup>
import { computed } from "vue";
import { VTextField } from "vuetify/components";
import store from "../src/store";

const meals = computed(() => store.state.meals);
const letters = "ABCDEFGHIJKLMNOPQRSTUVWXYZ"
</script>
