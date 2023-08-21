<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityCard from "./CityCard.vue";

const savedCities = ref([]);
const apiKey = import.meta.env.VITE_WEATHER_API_KEY;

const getCities = async () => {
    // ensure we have the city stored inside local storage
    if (localStorage.getItem("savedCities")) {
        savedCities.value = JSON.parse(localStorage.getItem("savedCities"));

        // retrieve current weather info then display in city card
        const requests = [];
        savedCities.value.forEach((city) => {
            requests.push(
                axios.get(
                    `http://api.weatherapi.com/v1/forecast.json?key=${apiKey}&q=${city.coords.lat},${city.coords.lng}&days=1&aqi=no&alerts=no`
                )
            );
        });

        const weatherData = await Promise.all(requests);
        console.log(weatherData);

        //Flicker Delay
        await new Promise((res) => setTimeout(res, 1000));

        weatherData.forEach((value, index) => {
            savedCities.value[index].weather = value.data;
        });
    }
};
await getCities();
console.log(savedCities);

const router = useRouter();

const goToCityView = (city) => {
    router.push({
        name: "cityView",
        params: { city: city.city, region: city.region },
        query: {
            id: city.id,
            lat: city.coords.lat,
            lng: city.coords.lng,
        },
    });
};

</script>

<template>
    <div v-for="city in savedCities" :key="city.id">
        <CityCard :city="city" @click="goToCityView(city)" />
    </div>

    <p v-if="savedCities.length === 0">
        No locations added. To start tracking a location, search in the field above.
    </p>
</template>