<script setup>
import { ref } from "vue";

defineProps({
  msg: String,
});

</script>

<template>
  <h1 class="bg-green-200 bg-opacity-50">{{ msg }}</h1>
</template>

<style scoped>
.read-the-docs {
  color: #888;
}
</style>
