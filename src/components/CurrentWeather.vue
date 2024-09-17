<script setup>
const props = defineProps({
    current: Object,
    location: Object
})
</script>
<template>
  <section class="text-center py-10">
    <h2 class="text-5xl text-white">{{location.name}}</h2>
    <p class="text-zinc-500 mt-4">{{location.admin1}}, {{ location.admin2 }}, {{ location.country }}</p>
    <h1 class="text-7xl mt-4">{{ current?.temperature }} <span class="text-lg">°C</span></h1>
    <div class="mt-4 flex justify-center gap-4">
        <div class="flex items-center gap-2">
            <img src="../assets/windy.png" width="24" alt="wind">
            <span>{{ current?.windSpeed || 'NA'}}Km/h</span>
        </div>
    </div>
  </section>
</template>