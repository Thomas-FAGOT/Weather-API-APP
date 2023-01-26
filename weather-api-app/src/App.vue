<template>

  <div class="header">
    <div class="logo">

    </div>
    <div class="name">
      <h1>Weather Forecast API</h1>
    </div>
  </div>
  <div class="content">
    <div class="searchBar">
      <input type="text" id="position"  placeholder="Ville" v-model="requete" v-on:keypress="goCity">
    </div>
    <div class="answer" id="answer_city" v-if="city">
      <h2> Infromation sur la ville </h2>
      <h3>Pays : {{ city.results[0].name }}</h3>
      <h4>Pays : {{ city.results[0].latitude }}</h4>
      <h4>Pays : {{ city.results[0].longitude }}</h4>
      <br>
    </div>
    <div class="answer" id="answer_weather" v-if="meteo">
      <h2> Information sur la météo </h2>
      <h3>Temps : {{ temps }}</h3>
      <h4>Température : {{ meteo.current_weather.temperature }}°C</h4>
    </div>
  </div>
  <div class="footer">

  </div>
</template>

<script>
import axios from 'axios';
export default {
  name: 'App',
  data(){
    return{
    requete: '',
    city: undefined,
    meteo: undefined,
    temps: undefined,
    url_Pays: 'https://geocoding-api.open-meteo.com/v1/search?name=',
    url_meteo: 'https://api.open-meteo.com/v1/forecast?',
    }
  },
  methods: {
    goCity(e){
      if (e.key == "Enter"){
        axios
        .get(`${this.url_Pays}${this.requete}&language=fr&count=1`)
        .then(reponse => {
          this.city = reponse.data
          console.log(this.city);
          axios
          .get(`${this.url_meteo}latitude=${this.city.results[0].latitude}&longitude=${this.city.results[0].longitude}&hourly=temperature_2m&current_weather=true`)
          .then(reponse => {
            this.meteo = reponse.data
            if (this.meteo.current_weather.weathercode == 0){
              this.temps = "Clear sky"
            } else if (this.meteo.current_weather.weathercode > 0 && this.meteo.current_weather.weathercode <= 3){
              this.temps = "Mainly clear, partly cloudy, and overcast"
            } else if (this.meteo.current_weather.weathercode > 44 && this.meteo.current_weather.weathercode <= 48){
              this.temps = "Fog and depositing rime fog"
            } else if (this.meteo.current_weather.weathercode > 50 && this.meteo.current_weather.weathercode <= 55){
              this.temps = "Drizzle: Light, moderate, and dense intensity"
            } else if (this.meteo.current_weather.weathercode > 55 && this.meteo.current_weather.weathercode <= 57){
              this.temps = "Freezing Drizzle: Light and dense intensity"
            } else if (this.meteo.current_weather.weathercode > 60 && this.meteo.current_weather.weathercode <= 65){
              this.temps = "Rain: Slight, moderate and heavy intensity"
            } else if (this.meteo.current_weather.weathercode > 65 && this.meteo.current_weather.weathercode <= 67){
              this.temps = "Freezing Rain: Light and heavy intensity"
            } else if (this.meteo.current_weather.weathercode > 70 && this.meteo.current_weather.weathercode <= 75){
              this.temps = "Snow fall: Slight, moderate, and heavy intensity"
            } else if (this.meteo.current_weather.weathercode == 77){
              this.temps = "Snow grains"
            } else if (this.meteo.current_weather.weathercode > 79 && this.meteo.current_weather.weathercode <= 82){
              this.temps = "Rain showers: Slight, moderate, and violent"
            } else if (this.meteo.current_weather.weathercode > 84 && this.meteo.current_weather.weathercode <= 86){
              this.temps = "Snow showers slight and heavy"
            } else if (this.meteo.current_weather.weathercode == 95){
              this.temps = "Thunderstorm: Slight or moderate"
            } else if (this.meteo.current_weather.weathercode > 95 && this.meteo.current_weather.weathercode <= 99){
              this.temps = "Thunderstorm with slight and heavy hail"
            } 
            console.log(this.meteo);
          })
          .catch(err => {
            console.error(err);
          })
        })
        .catch(err => {
          console.error(err);
        });

        this.requete = ''
      }
    }
  },
}
</script>