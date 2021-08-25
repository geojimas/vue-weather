<template>
  <div id="app" :class="bg ? '' : 'warm'">
    <main class="animate__animated animate__zoomIn">
      <div class="search-box">
        <input
          type="text"
          v-model="query"
          class="search-bar"
          placeholder="Search..."
          @keyup.enter="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="loading">
        <div class="location-box">
          <div class="location">
            <h2>{{ weather.name }}, {{ weather ? weather.sys.country : '' }}</h2>
          </div>
          <div class="date">
            <h3>{{ date }}</h3>
          </div>
        </div>

        <div class="weather-box">
          <div class="temp">
            <h2>{{ Math.round(weather ? weather.main.temp : '') }}°c</h2>
          </div>
          <div class="weather">
            <h1>{{ weather.weather[0].description }}</h1>
          </div>
          <div class="wind">
            <h4>Min Temp : {{ Math.round(weather.main.temp_min) }}°c</h4>
            <h4>Wind Speed : {{ weather.wind.speed }} mph</h4>
          </div>
        </div>
      </div>
      <div v-else class="else">
        <h1>Type the location and see the weather in there!</h1>
      </div>
    </main>
  </div>
</template>

<script>
import { reactive, ref } from 'vue'
import axios from 'axios'
import moment from 'moment'
import Swal from 'sweetalert2'

export default {
  name: 'App',

  setup () {
    const api = reactive({
      key: process.env.VUE_APP_API_KEY,
      url_base: process.env.VUE_APP_URL_BASE
    })

    const query = ref('')
    const weather = ref([])
    const date = moment().format('MMMM Do YYYY, h:mm:ss a')
    const loading = ref(false)
    const bg = ref(false)

    // fetch Method
    const fetchWeather = () => {
      axios
        .get(`${api.url_base}weather?q=${query.value}&units=metric&APPID=${api.key}`)
        .then(response => {
          loading.value = true
          weather.value = response.data
          if (weather.value.main.temp < 16) {
            bg.value = false
          } else {
            bg.value = true
          }
        })
        .catch(error => {
          Swal.fire({
            icon: 'error',
            title: 'City not Found',
            text: error.message
          })
        })
    }

    return {
      query,
      weather,
      fetchWeather,
      date,
      loading,
      bg
    }
  }
}
</script>
