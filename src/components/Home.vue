<template>
    
    <div :style="{ backgroundImage: computedBackground }" class="home-background">
        <!-- Header Information with Title etc -->
        <h1 class="app-title">Weather Highlights App</h1>
        <div>
        <div class="search-box">
            <input 
            type="text" 
            class="search-bar" 
            placeholder="Enter a location..."
            v-model="query"
            @keypress="fetchWeather"
            />
        </div>

        <!-- Save Button -->
        <div v-if="weather.name" class="save-button-wrap">
                <button @click="saveLocation" class="save-button">🤍 Save This Location</button>
        </div>

        <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
            <div class="location-box">
            <div class="location">{{ locationFull }}</div>
            <br>
            <div class="date">{{ dateBuilder() }}</div>
            </div>
    
            <div class="weather-box">
            <div class="temp">{{ Math.round(weather.main.temp) }}°F</div>
            <div class="weather">{{ weather.weather[0].main }}</div>
            </div>

        </div>
        </div>

        <!-- Hourly Forecast Chart -->
        <div class="section">
        <HourlyForecast />
        </div>

        <!-- 5 Day Forecast Chart -->
        <div class="section">
            <FiveDay />
        </div>
        <!-- Saved Locations Section -->
        <SavedLocations :onSelect="handleLocationClick" ref="savedList" />
        <footer class="app-footer">
            © 2025 Weather Highlights App. All rights reserved.
        </footer>
    </div>
  </template>
  
  <script>
    import HourlyForecast from './Hourly.vue'
    import FiveDay from './FiveDay.vue'
    import SavedLocations from './SavedLocations.vue'
    import coldImg from '../assets/cold-image.jpg'
    import warmImg from '../assets/warm-image.jpg'
    import clearImg from '../assets/clear-image.jpg'
    import rainImg from '../assets/rain-image.jpg'

    // Forecast Fetch
    export default {
    name: 'WeatherHome',
    components: {
        HourlyForecast,
        FiveDay,
        SavedLocations
    },
    
    // API information here //
    data () {
        return {
        api_key: '5b596f8c166b05acd7402a2644c90a11',
        url_base: 'https://api.openweathermap.org/data/2.5/',
        query: '',
        weather: {},
        locationFull: ''
        }
    },
    
    // Background images and respective changes //
    computed: {
        computedBackground() {
        if (!this.weather || !this.weather.weather) {
            return `url(${clearImg})` // default background
        }

        const condition = this.weather.weather[0].main.toLowerCase()
        const temp = this.weather.main.temp

        if (condition.includes('rain') || condition.includes('drizzle')) {
            return `url(${rainImg})`
        } else if (condition.includes('clear')) {
            return temp >= 75
            ? `url(${warmImg})`
            : `url(${clearImg})`
        } else if (temp <= 45) {
            return `url(${coldImg})`
        } else {
            return `url(${clearImg})`
        }
        }
    },
    
    methods: {
        async fetchWeather(e) {
        if (e.key === "Enter") {
            try {
            const geoUrl = `https://api.openweathermap.org/geo/1.0/direct?q=${this.query}&limit=1&appid=${this.api_key}`
            const geoRes = await fetch(geoUrl)
            const geoData = await geoRes.json()

            if (!geoData[0]) {
                alert('Location not found!')
                return
            }

            const { lat, lon, state, country } = geoData[0]
            const typedCity = this.query.trim().split(' ')
                .map(word => word.charAt(0).toUpperCase() + word.slice(1).toLowerCase())
                .join(' ')

            this.locationFull = `${typedCity}${state ? ', ' + state : ''}, ${country}`

            const weatherUrl = `${this.url_base}weather?lat=${lat}&lon=${lon}&units=imperial&appid=${this.api_key}`
            const weatherRes = await fetch(weatherUrl)
            const weatherData = await weatherRes.json()

            this.setResults(weatherData)
            } catch (err) {
            console.error('Error fetching location/weather:', err)
            }
        }
        },
        
        setResults(results) {
        this.weather = results;
        },
        
        dateBuilder() {
        const d = new Date();
        const months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
        const days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];

        const day = days[d.getDay()];
        const date = d.getDate();
        const month = months[d.getMonth()];
        const year = d.getFullYear();

        return `${day}, ${month} ${date}, ${year}`;
        },
        
        handleLocationClick(city) {
        this.query = city
        this.fetchWeather({ key: 'Enter' })
        },
        
        saveLocation() {
        if (this.locationFull) {
            this.$refs.savedList.addLocation(this.locationFull)
        } else {
            alert('No location to save yet!')
        }
       }
      }
    }
</script>

  <style scoped> 
  /* Background Styling */
  .home-background {
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  transition: background-image 0.5s ease-in-out;
  min-height: 100vh;
  padding: 25px;
  background-blend-mode: overlay;
  background-color: rgba(0, 0, 0, 0.5); /* dark overlay */
 }

  .app-title {
  text-align: center;
  color: white;
  font-family: 'Raleway', sans-serif;
  font-size: 36px;
  margin: 30px 0;
 }

 /* Search Styling */
  .search-box {
    width: 100%;
    display: flex;
    justify-content: center;
    margin-bottom: 30px;
  }

  .search-box .search-bar {
    width: 65%;
    margin: 0 auto;
    padding: 15px;
    color: rgb(20, 20, 20);
    font-size: 20px;
    appearance: none;
    border: none;
    outline: none;
    background: none;
    background-color: rgba(255, 255, 255, 0.69);
    border-radius: 0px 16px 0px 16px;
    transition: 0.4s;
    box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  }

  .search-box .search-bar:focus {
    box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
    background-color: rgba(255, 255, 255, 0.85);
    border-radius: 16px 0px 16px 0px;
  }

  /* Location Styling */
  .location-box .location {
    color: #ffffff;
    font-size: 32px;
    font-weight: 500;
    text-align: center;
    text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
  }

  .location-box .date {
    color: #ffffff;
    font-size: 20px;
    font-weight: 300;
    font-style: italic;
    text-align: center;
  }
  
  /* Weather Temp Styling */
  .weather-box {
    text-align: center;
  }
  
  .weather-box .temp {
    display: inline-block;
    padding: 10px 25px;
    color: #ffffff;
    font-size: 102px;
    font-weight: 900;
    text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
    background-color: rgba(255, 255, 255, 0.25);
    border-radius: 16px;
    margin: 30px 0px;
    box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
    font-variant-numeric: tabular-nums;
  }
  
  .weather-box .weather {
    color: #ffffff;
    font-size: 48px;
    font-weight: 700px;
    font-variant: small-caps;
    letter-spacing: 1.5px;
    text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  }
  
  /* Charts' Styling */
  .section {
    margin-top: 40px;
    display: flex;
    justify-content: center;
    text-align: center;
  }

  .save-button-wrap {
  text-align: center;
  margin: 10px 0 20px 0;
  }

  .save-button {
  background-color: rgba(255, 255, 255, 0.2);
  color: white;
  font-size: 16px;
  padding: 8px 16px;
  border: 1px solid white;
  border-radius: 10px;
  cursor: pointer;
  transition: 0.3s;
  font-family: 'Roboto', sans-serif;
 }

 .save-button:hover {
  background-color: rgba(255, 255, 255, 0.4);
 }

 .app-footer {
  text-align: center;
  color: #ffffff;
  font-size: 14px;
  margin-top: 50px;
  padding: 20px 0;
  opacity: 0.7;
  font-family: 'Roboto', sans-serif;
}
  </style>
  