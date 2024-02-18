<script setup lang="ts">
import SiteNavigation from '@/components/SiteNavigation.vue'
import { RouterView } from "vue-router";
</script>

<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <SiteNavigation></SiteNavigation>
    <RouterView ></RouterView>
  </div>
</template>

<style scoped></style>
