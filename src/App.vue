<script setup lang="ts">
import SiteNavigation from '@/components/SiteNavigation.vue'
import { RouterView } from "vue-router";
</script>

<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <SiteNavigation></SiteNavigation>
    <RouterView v-slot="{Component} ">
      <Transition name="page" mode="out-in">
        <component :is="Component"></component>
      </Transition>
    </RouterView>
  </div>
</template>

<style>

.page-enter-active {
  transition: 600ms ease all;
}

.page-enter-from {
  opacity: 0;
}

</style>
