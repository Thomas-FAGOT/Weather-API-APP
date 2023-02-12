<template>
  <link rel="stylesheet" href="https://fonts.googleapis.com/css2?family=Material+Symbols+Outlined:opsz,wght,FILL,GRAD@48,400,0,0" />
  <div class="header">
    <div class="Action" id="Action_geolocalisation">
      <button v-on:click="meteoApiGeolocalisationAuto"> Météo </button>
    </div>
    <div class="searchBar">
      <div class="searchBarInput">
        <span class="material-symbols-outlined">search</span>
        <input type="text" id="position" placeholder="Ville" v-model="requete" @input="meteoApi">
      </div>
      <ul v-if="cityList != null">
        <li v-for="city in cityList" :key="city.id" @click="test(city)"> {{ city.name }} - {{ city.admin1 }} - {{ city.country }}</li>
      </ul>
    </div>
  </div>
  <div class="content">
    <div class="answer" id="answer">
      <div class="answer_title" v-if="citySelect != null">
        <h2> météo du jour à {{ citySelect.name }} </h2>
      </div>
      <div class="answer_title" v-if="citySelect == null">
        <h2> météo du jour chez vous </h2>
      </div>      
      <div class="answer" id="answer_weather" v-if="meteo">
        <h3>Temps : {{ temps }}</h3>
        <h4>Température : {{ meteo.current_weather.temperature }}°C</h4>
        <h4>Vitesse du vent : {{ meteo.current_weather.windspeed }}Km/h</h4>
      </div>
      <div class="answer_weather_presionnel">
        <div class="answer_title">
          <h2> Prévision des 3 prochains jours </h2>
        </div>
        <div class="answer" id="answer_weather_previsionnel_0" v-if="meteo">
          <h3>{{ meteo.daily.time[0] }}</h3>
          <h4>Température max : {{ meteo.daily.temperature_2m_max[0] }}</h4>
          <h4>Température min : {{ meteo.daily.temperature_2m_min[0] }}°C</h4>
        </div>
        <div class="answer" id="answer_weather_previsionnel_1" v-if="meteo">
          <h3>{{ meteo.daily.time[1] }}</h3>
          <h4>Température max : {{ meteo.daily.temperature_2m_max[1] }}</h4>
          <h4>Température min : {{ meteo.daily.temperature_2m_min[1] }}°C</h4>
        </div>
        <div class="answer" id="answer_weather_previsionnel_2" v-if="meteo">
          <h3>{{ meteo.daily.time[2] }}</h3>
          <h4>Température max : {{ meteo.daily.temperature_2m_max[2] }}</h4>
          <h4>Température min : {{ meteo.daily.temperature_2m_min[2] }}°C</h4>
        </div>
      </div>
    </div>
  </div>
  <div class="footer">
  </div>
</template>

<script>
import '@/assets/main.css';
import axios from 'axios';
export default {
  name: 'App',
  data(){
    return{
    requete: '',
    cityList: null,
    citySelect: null,
    meteo: null,
    temps: null,
    favoris: null,
    favorisList: [],
    url_Pays: 'https://geocoding-api.open-meteo.com/v1/search?name=',
    url_meteo: 'https://api.open-meteo.com/v1/forecast?',
    }
  },
  methods: {

    test(city){
      this.citySelect = city
      console.log(this.citySelect);
      axios       
        .get(`${this.url_meteo}latitude=${this.citySelect.latitude}&longitude=${this.citySelect.longitude}&daily=temperature_2m_max,temperature_2m_min&current_weather=true&timezone=auto&start_date=${this.defDate(1)}&end_date=${this.defDate(3)}`)
        .then(reponse => {
          this.meteo = reponse.data
          this.defWeatherCode();
        })
        .catch(err => {
          console.error(err);
        })
      this.cityList = null
      this.requete = ''
    },

    defDate(jour){
      var date = new Date();
      date.setDate(date.getDate() + jour);
      var getYear = date.toLocaleString("default", { year: "numeric" });
      var getMonth = date.toLocaleString("default", { month: "2-digit" });
      var getDay = (date.toLocaleString("default", { day: "2-digit" }));
      var current_date = getYear + "-" + getMonth + "-" + getDay;
      return current_date;
    },

    defWeatherCode(){
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
    },


    meteoApi(){
//-------------------------------------------------------------------------------------------//
      axios
      .get(`${this.url_Pays}${this.requete}&language=fr`)
      .then(reponse => {
        this.cityList = reponse.data.results
        console.log("methode : meteoAPI \nListe des villes \n")
        console.log(this.cityList)
      })
      .catch(err => {
        console.error(err);
      }); 
    },

    meteoApiGeolocalisationAuto(){
      // Déclaration de variable
      this.citySelect = null
      navigator.geolocation.getCurrentPosition((position) =>{
        axios
        .get(`${this.url_meteo}latitude=${position.coords.latitude}&longitude=${position.coords.longitude}&daily=temperature_2m_max,temperature_2m_min&current_weather=true&timezone=auto&start_date=${this.defDate(1)}&end_date=${this.defDate(3)}`)
        .then(reponse => {
          this.meteo = reponse.data
          this.defWeatherCode();
        })
        .catch(err => {
          console.error(err);
        })
      })
    },

    /*
    //Favoris

    favorisAdd(){
      this.favorisList.push(this.citySelect)
      console.log(this.favorisList);
    },

    favorisDelete(){
      this.favorisList.pop(this.citySelect)
    }

    <div class="Action" id="Action_favoris">
      <button v-on:click="favorisAdd"> Ajouter aux favoris </button>
      <button v-on:click="favorisDelete"> Supprimer des favoris </button>
      <h2>Favoris</h2>
      <ul>
        <li v-for="favoris in favorisList" :key="favoris"> {{ favoris.name }}</li>
      </ul>
    </div>
    */
  },
  beforeMount(){
    this.meteoApiGeolocalisationAuto()
  },
}
</script>