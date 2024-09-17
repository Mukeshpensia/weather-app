<script setup>
const props = defineProps({
    forecast: Array
})
</script>
<template>
    <section>
        <h2 class="text-2xl mt-8">Daily Forcast</h2>
        <ul class="grid grid-cols-2 md:grid-cols-5 gap-4">
            <li class="glass-bg p-3 min-w-36 mt-4 rounded-md" v-for="(day, index) in props.forecast" :key="index">
                <h3 class="text-sm text-zinc-300">{{ new Intl.DateTimeFormat('en-US', {
                    day: 'numeric', month:
                        'short'
                }).format(new Date(day.date)) }}</h3>
                <div>
                    <img :src="`https://open-meteo.com/static/images/weather/${day.icon}.png`" class="w-full" alt="">
                </div>
                <div class="mt-2">
                    <p class="text-3xl text-center">{{ day.low }}<span class="text-sm ">°C <span
                                class="text-blue-500">low</span></span></p>
                    <p class="text-3xl text-center">{{ day.high }} <span class="text-sm ">°C <span
                                class="text-rose-500">high</span></span></p>
                </div>
                <div class="mt-4 flex justify-center gap-4">
                    <div class="flex items-center gap-2">
                        <img src="../assets/windy.png" width="24" alt="wind">
                        <span>wind | {{ day?.windSpeed || 'NA' }} Km/h</span>
                    </div>
                </div>
            </li>
        </ul>
    </section>
</template>