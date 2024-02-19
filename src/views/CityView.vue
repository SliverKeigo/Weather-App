
<template>
    <div>
        <Suspense>
            <AsyncCityView></AsyncCityView>
            <template #fallback>
                <CityViewSkeletonVue></CityViewSkeletonVue>
            </template>
        </Suspense>
    </div>
</template>

<script setup lang="ts">
import CityViewSkeletonVue from '@/components/CityViewSkeleton.vue';
import AsyncCityView from '../components/AsyncCityView.vue';


</script>

