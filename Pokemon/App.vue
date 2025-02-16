<script setup>
import PokemonList from "./Pokemon/PokemonList.vue";
</script>

<template>
  <div class="container">
    <h1>Pokemon API</h1>
    <PokemonList />
  </div>
</template>

<style scoped>
.container {
  text-align: center;
  margin-top: 20px;
}
h1 {
  color: #2c3e50;
  font-size: 36px;
}
</style>
