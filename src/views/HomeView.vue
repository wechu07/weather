<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityList from "../components/CityList.vue";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";

const mapboxAPIKey = import.meta.env.VITE_MAPBOX_API_KEY;

const router = useRouter();

const previewCity = (searchResult) => {
  console.log(searchResult);
  let [city, region] = searchResult.place_name.split(",");
  region = region.replaceAll(" ", "");
  console.log(city, region);

  router.push({
    name: "cityView",
    params: { region: region, city: city },
    query: {
      // for example
      /**
       * {
      "type": "Point",
      "coordinates": [
                36.438783,
                -0.722442
              ]
        }
       */
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true // just previewing
    }
  })
}

const searchQuery = ref("");
const queryTimeout = ref(null);
const searchError = ref(false);
const mapboxSearchResults = ref(null);
const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`)
        mapboxSearchResults.value = result.data.features;
        console.log(mapboxSearchResults.value)
      } catch {
        searchError.value = true;
      }
      return;
    }
    mapboxSearchResults.value = null;
  }, 300)
}
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="Search for a city or region"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-primary focus:outline-none focus:shadow-[0px_1px_0_0_#588157]">
      <ul class="absolute bg-primary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="mapboxSearchResults">
        <p class="py-2" v-if="searchError">We probably need to try again, something went wrong</p>
        <p class="py-2" v-if="!searchError && mapboxSearchResults.length === 0">No results match your query, try a
          different place</p>
        <template v-else>
          <li v-for="searchResult in mapboxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer"
            @click="previewCity(searchResult)">
            {{ `${searchResult.place_name}` }}
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
