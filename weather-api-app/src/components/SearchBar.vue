<template>
    <div class="search-bar">
      <input type="text" v-model="query" @keyup="search" placeholder="Rechercher une ville" />
    </div>
  </template>

<script>
import axios from 'axios';

export default {
  name: 'SearchBar',
  data() {
    return {
      query: ''
    }
  },
  methods: {
    search() {
      // Émettez l'événement de recherche ici en utilisant `this.$emit('search', this.query)`
    }
  }
}
</script>