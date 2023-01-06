<template>
  <div class="hello">
    <div>
      <h1> altitude:{{ altitude }}</h1>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'HelloWorld',
  data(){
    return{
      altitude: null,
    }
  },
  mounted(){
    axios
    .get('https://api.open-meteo.com/v1/forecast?latitude=52.52&longitude=13.41&hourly=temperature_2m')
    .then((reponse) => {
      this.altitude = reponse.data.latitude
      console.log(this.altitude)
    })
  }
}
</script>

<style scoped>
</style>
