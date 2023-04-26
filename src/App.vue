<template>
  <main>
    <div class="search-box">
      <input
        type="text"
        class="search-bar"
        placeholder="Search..."
        v-model.trim="query"
        @keypress="fetchWeather"
      />
    </div>
    <div class="weather-wrap" v-if="weather.main">
      <div class="location-box">
        <div class="location">
          {{ weather.name }}, {{ weather.sys.country }}
        </div>
        <div class="date">{{ dateBuilder() }}</div>
      </div>
      <div class="weather-box">
        <div class="temp">{{ Math.round(weather.main.temp) }}°</div>
        <div class="weather">{{ weather.weather[0].main }}</div>
      </div>
    </div>
  </main>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      api_key: 'ad9b6dedcff0af31d587672a2604e2c7',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {}
    }
  },
  methods: {
    async fetchWeather(e) {
      if (e.key === 'Enter') {
        try {
          const res = await fetch(
            `${this.url_base}weather?q=${this.query}&units=metric&appid=${this.api_key}`
          )
          if (res.status === 200) {
            const data = await res.json()
            this.setResults(data)
          } else if (res.status === 404) {
            alert(`City ${this.query} not found`)
          }
        } catch (error) {
          alert('Error server')
        } finally {
          this.query = ''
        }
      }
    },
    setResults(results) {
      this.weather = results
      if (this.weather?.main?.temp > 16) {
        document.getElementById('app').classList.add('warm')
      } else if (
        this.weather?.main?.temp < 16 &&
        document.getElementById('app').classList.contains('warm')
      ) {
        document.getElementById('app').classList.remove('warm')
      }
    },
    dateBuilder() {
      const currentDay = new Date()
      const formatter = new Intl.DateTimeFormat('en-US', {
        year: 'numeric',
        month: 'long',
        day: 'numeric',
        weekday: 'long'
      })
      return formatter.format(currentDay)
    }
  }
}
</script>

<style setup>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: 'Montserrat', sans-serif;
}
#app {
  background-image: url('./assets/cold-bg.jpg');
  background-size: contain;
  background-position: bottom;
  transition: 0.4s;
}
@media (max-width: 767.98px) {
  #app {
    background-size: cover;
  }
}
#app.warm {
  background-image: url('./assets/warm-bg.jpg');
}
main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}
.search-box {
  width: 100%;
  margin-bottom: 30px;
}
.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}
.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}
.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}
.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}
.weather-box {
  text-align: center;
}
.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
