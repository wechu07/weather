<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";

const apiKey = import.meta.env.VITE_WEATHER_API_KEY;

const route = useRoute();
const getWeatherData = async () => {
  try {
    const forecastWeatherData = await axios.get(`http://api.weatherapi.com/v1/forecast.json?key=${apiKey}&q=${route.query.lat},${route.query.lng}&days=1&aqi=no&alerts=no`);

    await new Promise((res) => setTimeout(res, 1000));

    const locationName = await forecastWeatherData.data.location.name;
    const localTime = await forecastWeatherData.data.location.localtime;
    const currentTemp = await forecastWeatherData.data.current.temp_c;
    const icon = await forecastWeatherData.data.current.condition.icon;
    const forecast = await forecastWeatherData.data.forecast.forecastday[0];
    const hourlyForecast = await forecast.hour;

    // The current UTC timestamp
    const nowUtcTimestamp = new Date().getTime();

    // Filter the hourly forecast to get only future hours
    const futureHourlyForecast = hourlyForecast.filter(hour => {
      const hourUtcTimestamp = new Date(hour.time_epoch * 1000).getTime();
      return hourUtcTimestamp > nowUtcTimestamp;
    });

    const parsedTime = new Date(localTime).toLocaleTimeString();

    return {
      locationName: locationName,
      currentTemp: currentTemp,
      localTime: parsedTime,
      icon: icon,
      forecast: forecast,
      hourlyForecast: futureHourlyForecast,
    };
  } catch (err) {
    console.log(err)
  }
}
const { locationName, currentTemp, localTime, icon, hourlyForecast } = await getWeatherData();
// console.log(hourlyForecast);
</script>

<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- the banner -->
    <div v-if="route.query.preview" class="text-white p-4 bg-primary w-full text-center">
      <p>
        You are currently previewing {{ locationName }}, click the "+" icon to start
        tracking this city.
      </p>
    </div>
    <!-- Weather Overview -->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-12">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">
        {{
          localTime
        }}
      </p>
      <p class="text-8xl mb-8">
        {{ currentTemp }}&deg;C
      </p>
      <img class="w-[150px] h-auto" :src="`http:${icon}`" alt="" />
    </div>

    <hr class="border-white border-opacity-10 border w-full" />

    <!-- Hourly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Hourly Weather</h2>
        <div class="flex gap-10 overflow-x-scroll">
          <div v-for="hour in hourlyForecast" :key="hour.time_epoch" class="flex flex-col gap-4 items-center">
            <p class="whitespace-nowrap text-md">
              {{ new Date(hour.time).toLocaleTimeString("en-us", {
                hour: "numeric",
              }) }}
            </p>
            <img class="w-auto h-[50px] object-cover"
              :src="`http:${hour.condition.icon}`" alt="" />
              <p class="text-xl">{{ Math.round(hour.temp_c) }}&deg;</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
