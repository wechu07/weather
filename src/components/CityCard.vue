<script setup>
defineProps({
  city: {
    type: Object,
    default: () => {},
  },
});
</script>

<template>
  <div
    class="flex py-6 px-3 bg-primary rounded-md shadow-md cursor-pointer"
  >
    <div class="flex flex-col flex-1">
      <h2 class="text-3xl">{{ city.city }}</h2>
      <h3>{{ city.region }}</h3>
    </div>

    <div class="flex flex-col gap-2">
      <p class="text-3xl self-end">
        {{ city.weather.current.condition.text }}
      </p>
      <div class="flex gap-2">
        <span class="text-xs">
          Temp:
          {{ city.weather.current.temp_c }}&deg;
        </span>
        <!-- <span class="text-xs">
          L:
          {{ Math.round(city.weather.main.temp_min) }}&deg;
        </span> -->
      </div>
    </div>
  </div>
</template>