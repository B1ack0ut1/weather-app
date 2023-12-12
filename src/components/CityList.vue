<template>
  <div v-for="city in savedCities" :key="city.id" class="mb-4">
    <CityCard :city="city" @click="goToCityView(city)" />
  </div>
  <p v-if="savedCities.length === 0">
    No locations added. To start tracking a location, search in the field above.
  </p>
</template>

<script setup>
import { ref } from "vue";
import CityCard from "./CityCard.vue";
import { useRouter } from "vue-router";
import axios from "axios";

const savedCities = ref([]);
const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));

    const requests = [];
    savedCities.value.forEach((city) => {
      const { lat, lng } = city.coords;

      requests.push(
        axios.get(
          `https://api.openweathermap.org/data/3.0/onecall?lat=${lat}&lon=${lng}&exclude={part}&appid=${
            import.meta.env.VITE_OPENWEATHER_API_KEY
          }&units=metric`
        )
      );
    });

    const weatherData = await Promise.all(requests);

    weatherData.forEach((value, index) => {
      savedCities.value[index].weather = value.data;
    });

    // Delay to deal with flicker
    await new Promise((res) => setTimeout(res, 500));
  }
};

await getCities();

const router = useRouter();
const goToCityView = (city) => {
  router.push({
    name: "cityView",
    params: { state: city.state, city: city.city },
    query: { id: city.id, lat: city.coords.lat, lng: city.coords.lng },
  });
};
</script>
