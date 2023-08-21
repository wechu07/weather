<template>
  <header class="sticky top-0 bg-secondary shadow-lg">
    <nav class="container flex flex-col sm:flex-row items-center gap-4 text-white py-6">
      <RouterLink :to="{ name: 'home' }">
        <div class="flex items-center gap-3">
          <i class="fa-solid fa-cloud text-2xl"></i>
          <p>Weather</p>
        </div>
      </RouterLink>

      <div class="flex gap-3 flex-1 justify-end">
        <i @click="toggleModal"
          class="fa-solid fa-circle-question text-xl hover:text-primary duration-150 cursor-pointer"></i>
        <i class="fa-solid fa-plus text-xl hover:text-primary duration-150 cursor-pointer" @click="addCity"
          v-if="route.query.preview"></i>
      </div>
      <InformationModal :modalActive="modalActive" @close-modal="toggleModal">
        <h1 class="text-black">Hey there</h1>
        <p class="text-black">I will add more things later</p>
      </InformationModal>
    </nav>
  </header>
</template>

<script setup>
import { ref } from "vue";
import { uid } from "uid";
import { RouterLink, useRoute, useRouter } from "vue-router";;
import InformationModal from "./InformationModal.vue";


const modalActive = ref(null);
const toggleModal = () => {
  modalActive.value = !modalActive.value;
};

const route = useRoute();
const router = useRouter();

const savedCities = ref([]);
const addCity = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }

  const locationObject = {
    id: uid(),
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };

  savedCities.value.push(locationObject);
  localStorage.setItem("savedCities", JSON.stringify(savedCities.value));

  let query = Object.assign({}, route.query);
  delete query.preview; // remove the query of preview
  query.id = locationObject.id
  router.replace({ query })
};
</script>
