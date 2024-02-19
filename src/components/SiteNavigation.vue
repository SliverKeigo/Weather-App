<template>
    <header class="sticky top-0 bg-weather-primary shadow-lg">
        <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6">
            <RouterLink :to="{ name: 'home' }">
                <div class="flex items-center gap-3 ">
                    <i class="fa-solid fa-sun text-2xl"></i>
                    <p class="text-2xl">The Local Weather</p>
                </div>
            </RouterLink>

            <div class="flex  gap-3 flex-1 justify-end">
                <i class="fa-solid fa-circle-info text-xl hover:text-weather-secondary duration-150 cursor-pointer"
                    @click="toggleModal"></i>
                <i class="fa-solid fa-plus text-xl
                 hover:text-weather-secondary duration-150 cursor-pointer" @click="addCity()"></i>
            </div>

            <BaseModal :modal-active="modalActive" @close-modal="toggleModal">
                <div class="text-black">
                    <h1 class="text-2xl mb-1">关于:</h1>
                    <p class="mb-4">
                        The Local Weather 允许您查看所选城市的当前和未来天气。
                    </p>
                    <h2 class="text-2xl">如何使用:</h2>
                    <ol class="list-decimal list-inside mb-4">
                        <li>
                            在搜索栏中输入城市名称进行搜索。
                        </li>
                        <li>
                            在搜索结果中选择一个城市，即可查看所选城市的当前天气情况。
                        </li>
                        <li>
                            点击右上角的 "+"图标跟踪城市。这将保存该城市，以便以后在主页上查看。
                        </li>
                    </ol>

                    <h2 class="text-2xl">删除城市</h2>
                    <p>
                        如果您不想再跟踪某个城市，只需在主页上选择该城市。在页面底部会有一个删除城市的选项。
                    </p>
                </div>
            </BaseModal>
        </nav>

    </header>
</template>

<script setup lang="ts">
import { RouterLink, useRoute, useRouter } from 'vue-router'
import BaseModal from './BaseModal.vue';
import { ref } from 'vue';
import { uid } from "uid";

const route = useRoute();
const router = useRouter();
let savedCities = ref([] as any[]);
let modalActive = ref();
function toggleModal() {
    modalActive.value = !modalActive.value;
}

function addCity() {
    if (localStorage.getItem('savedCities')) {
        savedCities.value = JSON.parse(localStorage.getItem('savedCities')!);
    };
    let locationObj = ref({
        id: uid(),
        city: route.params.city,
        cityId: route.query.id
    });
    // 判断是否已经存在相同 cityId 的城市
    // 遍历savedCitiesh获取所有cityid存到数组中
    let cityIds = savedCities.value.map(item =>  item._value.cityId);
    // 判断locationObj.value.cityId是否在数组中
    let flag = cityIds.includes(locationObj.value.cityId)

    if (flag) {
       // 如果已经存在该城市，则直接返回
        console.log("该城市已经存在");
        return;
    } else {
        savedCities.value.push(locationObj);
        localStorage.setItem('savedCities', JSON.stringify(savedCities.value));

        let query = Object.assign({}, route.query);
        delete query.preview;
        router.replace({
            query: query
        });
    }

}


</script>

