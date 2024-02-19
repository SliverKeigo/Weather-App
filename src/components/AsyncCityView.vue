<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- Banner -->
        <div v-if="route.query.preview" class="text-white p-4 bg-weather-secondary w-full text-center">
            <p>
                您正在预览该城市，点击"+"图标开始追踪该城市。
            </p>
        </div>
        <!-- Weather -->
        <div class="flex flex-col items-center text-white py-12">
            <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
            <p class="text-sm mb-12">
                {{
                    new Date().toLocaleDateString(
                        "en-us",
                        {
                            timeZone: "Asia/Shanghai", // 你的时区
                            weekday: "short",
                            day: "2-digit",
                            month: "long",
                        }
                    )
                }}
                {{
                    new Date().toLocaleTimeString(
                        "en-us",
                        {
                            timeZone: "Asia/Shanghai", // 你的时区
                            timeStyle: "short",
                        }
                    )
                }}
            </p>
            <p class="text-8xl mb-8">
                {{ Math.round(currentWeatherData.now.temp) }}&deg;
            </p>
            <p>
                体感温度
                {{ Math.round(currentWeatherData.now.feelsLike) }} &deg;
            </p>
            <p class="capitalize text-center">
                {{ currentWeatherData.now.text }}
            </p>
            <img class="w-[50px] h-auto" :src="`/node_modules/qweather-icons/icons/${currentWeatherData.now.icon}.svg`"
                alt="" />
        </div>
        <hr class="border-white border-opacity-10 border w-full" />
        <!-- Hourly Weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-6">小时天气</h2>
                <div class="flex gap-10 ">
                    <div v-for="hourData in hourlyData" :key="hourData.fxTime" class="flex flex-col gap-4 items-center">
                        <p class="whitespace-nowrap text-md">
                            {{
                                new Date(
                                    hourData.fxTime
                                ).toLocaleTimeString("en-us", {
                                    hour: "numeric",
                                })
                            }}
                        </p>
                        <img class="w-[25px] h-[25px] object-cover"
                            :src="`/node_modules/qweather-icons/icons/${hourData.icon}.svg`" alt="" />
                        <p class="text-xl">
                            {{ Math.round(hourData.temp) }}&deg;
                        </p>
                    </div>
                </div>
            </div>
        </div>
        <hr class="border-white border-opacity-10 border w-full" />

        <!-- Weekly Weather -->
        <div class="max-w-screen-md w-full py-12">
            <div class="mx-8 text-white">
                <h2 class="mb-6">七天天气</h2>
                <div v-for="day in sevenDaysData.daily" :key="day.fxDate" class="flex items-center mt-6">
                    <p class="flex-1">
                        {{
                            new Date(day.fxDate).toLocaleDateString(
                                "zh-cn",
                                {
                                    weekday: "long",
                                }
                            )
                        }}
                    </p>
                    <img class="w-[25px] h-[25px] object-cover" :src="`/node_modules/qweather-icons/icons/${day.iconDay}.svg`" alt="" />
                    <div class="flex gap-2 flex-1 justify-end">
                        <p>最高温度: {{ Math.round(day.tempMax) }}</p>
                        <p>最低温度: {{ Math.round(day.tempMin) }}</p>
                    </div>
                </div>
            </div>
        </div>

    </div>
</template>

<script setup lang="ts">
import Api from '@/global/api';
import axios from 'axios';
import { useRoute } from 'vue-router';

const route = useRoute();
async function getWeatherData() {
    try {
        // 七天
        let url = `https://devapi.qweather.com/v7/weather/7d?location=${route.query.id}&key=${Api}`
        // 当前
        let nowUrl = `https://devapi.qweather.com/v7/weather/now?location=${route.query.id}&key=${Api}`
        // 24小时
        let HoursUrl = `https://devapi.qweather.com/v7/weather/24h?location=${route.query.id}&key=${Api}`
        // 24小时

        const [sevenDaysResponse, currentWeatherResponse, hoursWeatherResponse] = await Promise.all([
            axios.get(url),
            axios.get(nowUrl),
            axios.get(HoursUrl)
        ]);
        return {
            sevenDaysData: sevenDaysResponse.data,
            currentWeatherData: currentWeatherResponse.data,
            hoursWeatherData: hoursWeatherResponse.data
        };
    } catch (error) {
        console.log(error);
        return null;
    }
}

const weatherData = await getWeatherData();

let sevenDaysData = weatherData?.sevenDaysData

let currentWeatherData = weatherData?.currentWeatherData

let hoursWeatherData = weatherData?.hoursWeatherData

let hourlyData = hoursWeatherData.hourly.slice(0, 9);
</script>
