<template>
  <div id="app">
    <form v-on:submit.prevent="onSubmit">
      <input type="text" placeholder="Enter your zip" ref="zip">
      <input type="submit" value="Submit">
  </form>

  <div v-if="hasWeather">
    <h2>{{this.weather.city}}</h2>
    <p>Current temperature: {{weather.temp}}</p>
    <p>Wind speed: {{weather.wind}}</p>
    <p v-if=weather.umbrella><strong>Bring umbrella</strong></p>
    <p v-if=weather.sunglasses><strong>Bring Sunglasses</strong></p>
  </div>

  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
const axios = require('axios')
export default {
  name: 'app',
  data() {
    return {
      weather: null
    }
  },
  mounted () {
    if (localStorage.zip) {
    this.getWeather(localStorage.zip);
}
  },
  computed: {
    hasWeather() {
      return this.weather !== null;
    }
  },
  methods: {
    onSubmit: function(event) {
      let zip = this.$refs.zip.value;
      localStorage.zip = zip;
      this.getWeather(zip);
    },
    getWeather(zip) {
      axios.get('https://api.openweathermap.org/data/2.5/forecast?zip='+zip+',us&units=imperial&APPID=f318ff623f24725c4aa5edbe57533535&APPID=ecfdec59568f8eba27b5af1b7bb76f88')
        .then(response => {
          let weather = response.data.list.slice(-1)[0];
          console.log(response);
          this.weather = {
            city: response.data.city.name,
            temp: weather.main.temp,
            wind: weather.wind.speed + 'mph',
            umbrella: weather.weather[0].id > 199 && response.data.list.slice(-1)[0].weather[0].id < 600,
            sunglasses: weather.weather[0].id > 799 || response.data.list.slice(-1)[0].weather[0].id == 802
          }
        })
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
