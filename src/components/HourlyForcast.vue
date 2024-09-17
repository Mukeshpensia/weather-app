<script setup>
const props = defineProps({
    hourly: Array
})
</script>
<template>
    <section>
        <h2 class="text-2xl">Hourly Forcast</h2>
        <div class="overflow-auto ">
            <ul class="flex gap-3">
                <li class="glass-bg p-3 min-w-36 mt-4 rounded-md" v-for="(hour, index) in props.hourly" :key="index">
                    <h3 class="text-sm text-zinc-300">{{ new Intl.DateTimeFormat('en-US', {
                        day: 'numeric', month:
                            'short', hour: 'numeric', hour12: true
                    }).format(new Date(hour.time)) }}</h3>
                    <div>
                        <img :src="`https://open-meteo.com/static/images/weather/${hour.weathercode}.png`"
                            class="w-full" alt="">
                    </div>
                    <div class="mt-2">
                        <p class="text-3xl text-center">{{ hour.temperature }} <span class="text-sm ">°C</span></p>
                    </div>
                    <div class="flex items-center gap-2 mt-3">
                        <img src="../assets/windy.png" width="24" alt="wind">
                        <span>{{ hour?.windSpeed || 'NA' }} Km/h</span>
                    </div>
                    <div class="flex items-center gap-2 mt-1">
                        <img src="../assets/hum.png" alt="humidity" width="24">
                        <span>{{ hour?.humidity || 'NA' }}%</span>
                    </div>
                </li>
            </ul>
        </div>
    </section>
</template>