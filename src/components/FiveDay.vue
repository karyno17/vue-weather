<template>
    <div class="five-day">
    <!-- Chart Title and Basic Chart Info -->
      <h2>5-Day Forecast</h2>
      <table v-if="forecast.length">
        <thead>
          <tr>
            <th>Day</th>
            <th>Condition</th>
            <th>High</th>
            <th>Low</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(day, index) in forecast.slice(0, 5)" :key="index">
            <td>{{ formatDay(day.dt) }}</td>
            <td>{{ day.weather[0].main }}</td>
            <td>{{ Math.round(day.temp.max) }}°F</td>
            <td>{{ Math.round(day.temp.min) }}°F</td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script>
  import { onMounted, ref } from 'vue'
  
  // 5 Day Forecast Fetch
  export default {
    name: 'FiveDayForecast',
    setup() {
      const forecast = ref([])
  
      const fetchForecast = async () => {
        const apiKey = '9016b5976e6c509b191855d825ab813a'
        const lat = 35.7796
        const lon = -78.6382
  
        const url = `https://api.openweathermap.org/data/3.0/onecall?lat=${lat}&lon=${lon}&exclude=hourly,minutely,current,alerts&units=imperial&appid=${apiKey}`
  
        try {
          const res = await fetch(url)
          const data = await res.json()
          forecast.value = data.daily
        } catch (err) {
          console.error('Error fetching 5-day forecast:', err)
        }
      }
  
      const formatDay = (timestamp) => {
        const date = new Date(timestamp * 1000)
        return date.toLocaleDateString('en-US', { weekday: 'long' })
      }
  
      onMounted(fetchForecast)
  
      return { forecast, formatDay }
    }
   }
  </script>
  
  <!-- Formatting for 5 Day Forecast Chart -->
  <style scoped>
  .five-day {
    width: 90%;
    max-width: 700px;
    margin: 40px auto;
    color: white;
    background-color: rgba(255, 255, 255, 0.1);
    padding: 20px;
    border-radius: 12px;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.2);
  }
  
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }
  
  th, td {
    padding: 10px;
    text-align: center;
    border-bottom: 1px solid rgba(255, 255, 255, 0.2);
  }
  
  th {
    color: #f0f0f0;
    font-weight: 600;
  }
  </style>
  