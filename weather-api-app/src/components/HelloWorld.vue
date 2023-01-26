<template>
  <div class="search-bar">
    <input type="text" v-model="query" @keyup="search" placeholder="Rechercher une ville" />
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'HelloWorld',
  data(){
    return{
      meteo: null,
      ville: null,
    }
  },
  mounted(){
    axios
    .get('https://geocoding-api.open-meteo.com/v1/search?name=rennes&language=fr')
    .then((reponse) => {
      this.ville = reponse.data.results[0].name
      console.log(this.ville)
    })
  },
  test(){
    axios
    .get('https://api.open-meteo.com//v1/meteofrance?latitude=48.11&longitude=-1.67')
    .then((reponse) => {
      this.meteo = reponse.data
      console.log(this.meteo.latitude)
    })
  }
}
</script>

