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
      <input type="text" id="position"  placeholder="test" v-model="requete" v-on:keypress="goCity">
    </div>
    <div class="answer" v-if="city">
      <h3>Pays : {{ city.results[0].name }}</h3>
      <h4>Pays : {{ city.results[0].latitude }}</h4>
      <h4>Pays : {{ city.results[0].longitude }}</h4>

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
    url_Pays: 'https://geocoding-api.open-meteo.com/v1/search?name=',
    }
  },
  methods: {
    goCity(e){
      if (e.key == "Enter"){
        axios
        .get(`${this.url_Pays}${this.requete}&language=fr&count=1`)
        .then(reponse =>{
          this.city = reponse.data
        })
        this.requete = ''
      }
    }
  },
}
</script>