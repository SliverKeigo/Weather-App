
<template>
    <div>
        <Suspense>
            <AsyncCityView></AsyncCityView>
            <template #fallback>
                <p class="text-center">加载中...</p>
            </template>
        </Suspense>
    </div>
</template>

<script setup lang="ts">
import AsyncCityView from '../components/AsyncCityView.vue';


</script>

