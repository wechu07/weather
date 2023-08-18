<script setup>
import { RouterView } from "vue-router";
import AppNavigation from "./components/AppNavigation.vue";
</script>

<template>
  <div class="flex flex-col min-h-screen font-Nunito bg-secondary">
    <AppNavigation/>
    <RouterView />
  </div>
</template>

