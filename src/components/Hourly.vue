<template>
     <!-- Hourly Chart Overview -->
    <div class="hourly">
      <h2>Hourly Forecast</h2>
      <canvas id="hourlyChart"></canvas>
    </div>
  </template>

  <script>
  import { onMounted, ref } from 'vue'
  import { Chart, registerables } from 'chart.js'
  
  Chart.register(...registerables)
  
  // Hourly Forecast Fetch
  export default {
    name: 'HourlyForecast',
    setup() {
      const hourlyTemps = ref([])
      const hourlyLabels = ref([])
  
      const fetchHourlyWeather = async () => {
        const apiKey = '9016b5976e6c509b191855d825ab813a'
        const lat = 35.7796 // Placeholder for Raleigh
        const lon = -78.6382
  
        const url = `https://api.openweathermap.org/data/3.0/onecall?lat=${lat}&lon=${lon}&exclude=daily,minutely,current,alerts&units=imperial&appid=${apiKey}`
  
        try {
          const res = await fetch(url)
          const data = await res.json()
  
          const hours = data.hourly.slice(0, 6) // next 6 hours
          hourlyTemps.value = hours.map(hour => hour.temp)
          hourlyLabels.value = hours.map(hour => {
            const date = new Date(hour.dt * 1000)
            return `${date.getHours() % 12 || 12} ${date.getHours() >= 12 ? 'PM' : 'AM'}`
          })
  
          renderChart()
  
        } catch (err) {
          console.error('Error fetching hourly weather:', err)
        }
      }
  
      const renderChart = () => {
        const ctx = document.getElementById('hourlyChart')
        new Chart(ctx, {
          type: 'line',
          data: {
            labels: hourlyLabels.value,
            datasets: [{
              label: 'Temperature (°F)',
              data: hourlyTemps.value,
              borderColor: '#ffffff', 
              backgroundColor: 'rgba(255, 255, 255, 0.2)',
              fill: true,
              tension: 0.4
            }]
          },
          options: {
            responsive: true,
            scales: {
                x: {
                    ticks: {
                        color: '#ccc'
                    }
                },
                y: {
                    ticks: {
                        color: '#ccc'
                    },
                    beginAtZero: false
              }
            },
            plugins: {
                legend: {
                    labels: {
                        color: '#fff'
                    }
                }
            }
          }
        })
      }
  
      onMounted(fetchHourlyWeather)
  
      return {}
    }
  }
  </script>
  
  <!-- Formatting for Hourly Forecast Chart -->
  <style scoped>
  .hourly {
    width: 90%;
    max-width: 800px;
    color: white;
    margin: 40px auto;
    padding: 20px;
    border-radius: 12px;
    box-shadow: 0 0 20px rgba(0, 0, 0, 0.2);
  }

  canvas {
    margin: auto;
    max-width: 100%;
    height: 300px;
  }
  </style>
  