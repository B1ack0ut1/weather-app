<template>
  <div>
    <Suspense>
      <AsyncCityView />
      <template #fallback>
        <p class="text-center text-white mt-8">Loading...</p>
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import AsyncCityView from "../components/AsyncCityView.vue";
</script>
