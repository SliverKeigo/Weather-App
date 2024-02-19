<template>
    <main class="container text-white">
      <div class="pt-4 mb-8 relative">
        <input
          type="text"
          v-model="searchQuery"
          @input="getSearchResults"
          placeholder="搜索城市"
          class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
        />
        <ul
          class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
          v-if="mapboxSearchResults"
        >
          <p class="py-2" v-if="searchError">
            抱歉，出错了，请稍后重试
          </p>
          <p
            class="py-2"
            v-if="!searchError && mapboxSearchResults.length === 0"
          >
            没有符合您搜索条件的结果，请尝试其他搜索条件。
          </p>
          <template v-else>
            <li
              v-for="searchResult in mapboxSearchResults"
              :key="searchResult.id"
              class="py-2 cursor-pointer"
              @click="previewCity(searchResult)"
            >
              {{ searchResult.place_name }}
            </li>
          </template>
        </ul>
      </div>
      <div class="flex flex-col gap-4">
        <Suspense>
          <CityList />
          <template #fallback>
            <CityCardSkeleton />
          </template>
        </Suspense>
      </div>
    </main>
  </template>
  
  <script setup lang="ts">
  import { ref } from "vue";
  import axios from "axios";
  import { useRouter } from "vue-router";
  import CityList from "../components/CityList.vue";
  
  const router = useRouter();
  const previewCity = (searchResult: any) => {
    const [city, state] = searchResult.place_name.split(",");
    router.push({
      name: "cityView",
      params: { state: state.replaceAll(" ", ""), city: city },
      query: {
        lat: searchResult.geometry.coordinates[1],
        lng: searchResult.geometry.coordinates[0],
        preview: "true",
      },
    });
  };
  
  const mapboxAPIKey =
    "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";
  const searchQuery = ref("");
  const queryTimeout = ref();
  const mapboxSearchResults = ref();
  const searchError = ref();
  const country = 'CN'; // 指定国家为中国
  const getSearchResults = () => {
    clearTimeout(queryTimeout.value);
    queryTimeout.value = setTimeout(async () => {
      if (searchQuery.value !== "") {
        try {
          const result = await axios.get(
            `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place&country=${country}`
          );
          mapboxSearchResults.value = result.data.features;
        } catch {
          searchError.value = true;
        }
  
        return;
      }
      mapboxSearchResults.value = null;
    }, 300);
  };
  </script>
  
 