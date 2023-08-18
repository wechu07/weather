<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";

const apiKey = import.meta.env.VITE_WEATHER_API_KEY;

const route = useRoute();
const getWeatherData = async () => {
    try {
        const weatherData = await axios.get(`https://api.weatherapi.com/v1/current.json?key=${apiKey}&q=${route.query.lat},${route.query.lng}&aqi=no`);

        // // calculate current date & time
        // const localOffset = new Date().getTimezoneOffset() * 60000;
        // const utc = weatherData.data.current.dt * 1000 + localOffset;
        // weatherData.data.currentTime =
        //     utc + 1000 * weatherData.data.timezone_offset;

        // // calculate hourly weather offset
        // weatherData.data.hourly.forEach((hour) => {
        //     const utc = hour.dt * 1000 + localOffset;
        //     hour.currentTime = utc + 1000 * weatherData.data.timezone_offset;
        // });

        await new Promise((res) => setTimeout(res, 1000));

        const locationName = weatherData.data.location.name;
        const conditionText = weatherData.data.current.condition.text;
        const currentTemp = weatherData.data.current.temp_c;
        const localTime = weatherData.data.location.localtime;

        return {
            locationName: locationName,
            conditionText: conditionText,
            currentTemp: currentTemp,
            localTime: localTime,
        };
    } catch (err) {
        console.log(err)
    }
}
const { locationName, conditionText, currentTemp, localTime } = await getWeatherData();
console.log(locationName, conditionText, currentTemp, localTime)
</script>

<template>
    <div class="flex flex-col flex-1 items-center">
        <!-- the banner -->
        <div v-if="route.query.preview" class="text-white p-4 bg-primary w-full text-center">
            <p>
                You are currently previewing this city, click the "+" icon to start
                tracking this city Currently, it is still not working.
            </p>
        </div>
        <!-- Weather Overview -->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-12">{{ route.params.city }}</h1>
      <p class="text-sm mb-12"> Where the time is
        {{
          localTime
        }}
      </p>
      <p class="text-sm mb-12">Sorry, I will update this to a more palatable version.</p>
      <p class="text-8xl mb-8"> It is
        {{ currentTemp }}&deg;
      </p>
      <p>I will add more detail on hourly forecast, weekly and all</p>
      <!-- <p>
        Feels like
        {{ Math.round(weatherData.current.feels_like) }} &deg;
      </p>
      <p class="capitalize">
        {{ weatherData.current.weather[0].description }}
      </p> -->
      <!-- <img
        class="w-[150px] h-auto"
        :src="`http://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
        alt=""
      /> -->
    </div>
    </div>
</template>
