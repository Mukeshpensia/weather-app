<script setup>
import { onClickOutside } from '@vueuse/core'
import { ref } from 'vue'
import DailyForcast from './components/DailyForcast.vue'
import HourlyForcast from './components/HourlyForcast.vue'
import CurrentWeather from './components/CurrentWeather.vue'
const API = 'https://geocoding-api.open-meteo.com/v1/search?name='
const searchQuery = ref("");
const searchResules = ref([]);
const weateher = ref(null);
const selectedResult = ref({
  "id": 1271685, "name": "Ganganagar", "latitude": 29.92009, "longitude": 73.87496, "elevation": 176, "feature_code": "PPLA2", "country_code": "IN", "admin1_id": 1258899, "admin2_id": 1271686, "timezone": "Asia/Kolkata", "population": 231838, "country_id": 1269750, "country": "India", "admin1": "Rajasthan", "admin2": "Ganganagar" 
});

const modal = ref(false)
const modalRef = ref(null)
onClickOutside(
  modalRef,
  (event) => {
    modal.value = false
  },
)

const SearchLocation = () => {
  if (!searchQuery.value) return
  try {
    const url = API + searchQuery.value
    fetch(url).then(res => res.json()).then(data => {
      searchResules.value = data.results
      modal.value = true
    })
  } catch (error) {
    console.log(error)
  }
}

const fetchWeather = (place) => {
  selectedResult.value = place
  modal.value = false
  if (selectedResult.value) {
    fetch(`https://api.open-meteo.com/v1/forecast?latitude=${selectedResult.value.latitude}&longitude=${selectedResult.value.longitude}&current_weather=true&temperature_unit=celsius&weathercode&precipitation_unit=inch&timezone=Asia%2FKolkata&forecast_days=5&hourly=temperature_2m,relativehumidity_2m,windspeed_10m,weathercode&models=icon_seamless&daily=weathercode,temperature_2m_max,temperature_2m_min,precipitation_sum,windspeed_10m_max`)
      .then(res => res.json())
      .then(data => {
        weateher.value = {
          city: selectedResult.value.name,
          current: {
            temperature: data.current_weather.temperature,
            description: data.current_weather.weathercode,
            humidity: data.current_weather.humidity,
            windSpeed: data.current_weather.windspeed,
            icon: `https://open-meteo.com/static/images/weather/${data.current_weather.weathercode}.png`
          },
          forecast: data.daily.weathercode.map((weathercode, index) => ({
            date: new Date(data.daily.time[index]).toLocaleDateString(),
            description: weathercode,
            icon: `https://open-meteo.com/static/images/weather/${weathercode}.png`,
            high: data.daily.temperature_2m_max[index],
            low: data.daily.temperature_2m_min[index],
            precipitation: data.daily.precipitation_sum[index],
            windSpeed: data.daily.windspeed_10m_max[index]
          })),
          hourly: data.hourly.time.map((time, index) => ({
            time: data.hourly.time[index],
            temperature: data.hourly.temperature_2m[index],
            humidity: data.hourly.relativehumidity_2m[index],
            windSpeed: data.hourly.windspeed_10m[index],
            description: data.hourly.weathercode[index],
            // icon: `https://open-meteo.com/static/images/weather/${data.hourly}.png`
          }))
        };
        console.log(weateher.value)
      })
      .catch(error => {
        console.error(error);
        weateher.value = {
          error: 'Failed to fetch weather data. Please try again later.'
        };
      })
  }
}
fetchWeather(selectedResult.value)
</script>

<template>
  <section class="weather-app text-white bg-cover bg-center relative h-screen">
    <div class="h-full overflow-auto px-6">
      <header class="flex items-center justify-between sticky top-0 border-b border-gray-700 py-4 z-10">
        <div class="font-semibold text-3xl">Weatehr App</div>
  
        <div class="relative mt-2 rounded-md shadow-sm">
          <input type="search" @focus="modal = true" id="search" name="search" v-model="searchQuery" @input="SearchLocation"
            class="glass-bg w-96 rounded-full pl-3 py-1.5" placeholder="Search...">
          <div class="absolute search-items top-full left-0 w-full glass-bg rounded-lg py-3 max-h-64 overflow-auto" ref="modalRef" v-if="modal && searchResules.length">
            <ul>
              <li role="button" v-for="(result, index) in searchResules" :key="index" @click="fetchWeather(result), selectedResult = result" class="py-1 px-3 hover:bg-white/10">
                <div>{{ result.name }}</div>
                <div class="text-sm text-zinc-400">{{result.admin1}}, {{ result.admin2 }}, {{ result.country }}</div>
              </li>
            </ul>
          </div>
        </div>
      </header>
      <main class="mt-6 relative pb-10">
        <CurrentWeather :current="weateher?.current" :location="selectedResult"/>
        <HourlyForcast :hourly="weateher?.hourly" />
        <DailyForcast :forecast="weateher?.forecast" />
      </main>
    </div>
  </section>
</template>

