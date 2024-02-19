<template>
    <main class="container text-white">
        <div class="pt-4 mb-8 relative">
            <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="搜索城市" class="py-2 px-1 w-full bg-transparent border-b
             focus:border-weather-secondary focus:outline-none
            focus:shadow-[0px_1px_0_0_#004E71]" />
            <ul v-if="searchResult" class="absolute bg-weather-secondary text-white w-full
            shadow-md py-2 px-1 top-[66px]">
                <p v-if="searchError">
                    抱歉，出错了，请稍后重试。
                </p>
                <p v-if="Object.keys(searchResult).length === 0 && !searchError">
                    没有符合您搜索条件的结果，请尝试其他搜索条件。
                </p>
                <template v-else>
                    <li v-for=" result in searchResult" :key="searchResult.id" class="py-2 cursor-pointer"
                        @click="previewCity(result)">

                        {{ result.name }}
                    </li>
                </template>
            </ul>
        </div>

    </main>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import axios from 'axios';
import type { City } from '@/interface/city';
import { useRouter } from 'vue-router';
import Api from '@/global/api';
// let Api = 'b0ba9afa21d34494a3f00e340cdbc259'
let searchQuery = ref("")
let queryTimeout = ref()
let searchResult = ref()
let searchError = ref()
let router = useRouter()
function getSearchResults() {
    clearTimeout(queryTimeout.value)
    searchError.value = false
    try {
        queryTimeout.value = setTimeout(async () => {
            if (searchQuery.value.length > 0) {
                try {
                    let { data } = await axios.get(`https://geoapi.qweather.com/v2/city/lookup?location=${searchQuery.value}&key=${Api}`)
                    if (data.location) {
                        searchResult.value = data.location
                    } else {
                        searchResult.value = {}
                    }
                    return;
                }
                catch (error) {
                    searchError.value = true
                    searchResult.value = {}
                }
            }
            searchResult.value = null;
        }, 300);
    } catch (error) {
        console.error('发生错误：', error);
    }
}


async function previewCity(result: City) {
    let cityName = result.name;
    router.push({
        name: "cityView",
        params: {city: cityName},
        query:{
            id:result.id,
            preview: "true"
        }
    })
}


</script>

<style scoped></style>