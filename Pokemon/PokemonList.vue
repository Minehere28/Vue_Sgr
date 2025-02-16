<script setup>
import { ref, computed } from "vue";

// Số lượng Pokémon hiển thị mỗi lần
const NUMBER_OF_RENDER = 12;
const offset = ref(0);
const pokemons = ref([]);
const filteredPokemons = ref([]);

// Lấy danh sách Pokémon từ API
async function fetchAPI(url) {
  const response = await fetch(url);
  const data = await response.json();
  return data;
}

async function getPokemon() {
  const data = await fetchAPI("https://pokeapi.co/api/v2/pokemon/?offset=0&limit=898");
  pokemons.value = data.results;
  filteredPokemons.value = pokemons.value;
  console.log("Fetched data:", pokemons.value);
}

// Gọi API ngay lập tức
getPokemon();

// Tính toán danh sách Pokémon hiển thị
const renderPokemons = computed(() => {
  return filteredPokemons.value.slice(0, offset.value + NUMBER_OF_RENDER);
});

// Lấy ID của Pokémon từ URL
function getIDPokemon(url) {
  const parts = url.split("/");
  return parseInt(parts[parts.length - 2], 10);
}

// Tải thêm Pokémon
function handleLoadMore() {
  offset.value += NUMBER_OF_RENDER;
}

// Tìm kiếm Pokémon
function handleSearch(event) {
  const searchTerm = event.target.value.toLowerCase();
  filteredPokemons.value = pokemons.value.filter((pokemon) => {
    return pokemon.name.includes(searchTerm);
  });
  offset.value = 0;
}
</script>

</script>

<template>
  <div class="list-container">
    <!-- Ô tìm kiếm -->
    <input class="search-input" type="text" placeholder="Search Pokémon..." @input="handleSearch" />

    <!-- Danh sách Pokémon -->
    <div class="items">
      <div v-for="pokemon in renderPokemons" :key="pokemon.name" class="item">
        <div class="item__id">#{{ getIDPokemon(pokemon.url) }}</div>
        <div
          class="item__image"
          :style="{
            backgroundImage: `url('https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${getIDPokemon(pokemon.url)}.png')`
          }"
        ></div>
        <div class="item__name">{{ pokemon.name }}</div>
      </div>
    </div>

    <!-- Nút Load More -->
    <button v-show="renderPokemons.length < filteredPokemons.length" class="load-more" @click="handleLoadMore">
      Load more
    </button>
  </div>
</template>

<style scoped>
/* Container chính */
.list-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 50px;
}

/* Ô tìm kiếm */
.search-input {
  width: 500px;
  padding: 15px;
  font-size: 16px;
  border-radius: 25px;
  border: 1px solid gray;
  margin-bottom: 20px;
}

/* Lưới Pokémon */
.items {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 15px;
  width: 100%;
  max-width: 1000px;
  padding: 0 20px;
}

.item {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 10px;
  border-radius: 10px;
  background: #fff;
  box-shadow: 0px 4px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s ease-in-out;
  cursor: pointer;
}

.item:hover {
  transform: scale(1.05);
}

.item__id {
  font-size: 14px;
  color: gray;
}

.item__image {
  width: 100%;
  aspect-ratio: 1 / 1;
  background-size: contain;
  background-repeat: no-repeat;
}

.item__name {
  font-size: 16px;
  font-weight: bold;
  text-transform: capitalize;
  margin-top: 5px;
}

/* Nút Load More */
.load-more {
  background-color: #ff4d4f;
  color: white;
  padding: 10px 20px;
  border-radius: 5px;
  border: none;
  margin-top: 20px;
  cursor: pointer;
}

.load-more:hover {
  background-color: #ff7875;
}
</style>
