<script setup>
import { onMounted, ref, watch, defineEmits } from "vue";
import { fetchAPI, getIDPokemon } from "../utils/getID";
import PokemonItem from "../PokemonItem/PokemonItem.vue";
import LoadMore from "../LoadMore/LoadMore.vue";

const props = defineProps({
    searchQuery: String,
});

const emit = defineEmits(["update:isSelect"]);

// const url = "https://pokeapi.co/api/v2/pokemon/?offset=0&limit=300";
let allPokemons = ref([]);
let displayedPokemons = ref([]);
let filteredPokemons = ref([]);
const offset = ref(0);
const batchSize = 36;
console.log(1111)

const getPokemon = async () => {
    const cachedPokemons = sessionStorage.getItem("allPokemons");
    console.log(cachedPokemons);
    console.log(1111)

    if (cachedPokemons) {
        allPokemons.value = JSON.parse(cachedPokemons);
        filterPokemons();
        return;
    }
    const pokemonList = fetchAPI(cachedPokemons.url)

    allPokemons.value = pokemonList;
    sessionfStorage.setItem("Pokemons", JSON.stringify(pokemonList));
    filterPokemons();

};




const filterPokemons = () => {
    if (!props.searchQuery || props.searchQuery.trim() === "") {
        filteredPokemons.value = allPokemons.value;
    } else {
        filteredPokemons.value = allPokemons.value.filter(pokemon =>
            pokemon.name.toLowerCase().includes(props.searchQuery.toLowerCase())
        );
    }
    offset.value = 0;
    displayedPokemons.value = filteredPokemons.value.slice(0, batchSize);
};

watch(() => props.searchQuery, () => {
    filterPokemons();
});

const handleLoadMore = () => {
    offset.value += batchSize;
    const nextBatch = filteredPokemons.value.slice(offset.value, offset.value + batchSize);
    displayedPokemons.value = [...displayedPokemons.value, ...nextBatch];
};

const handleSelectPokemon = (pokemon) => {
    sessionStorage.setItem("selectedPokemon", JSON.stringify(pokemon)); // 🟢 Lưu cả Pokémon vào `sessionStorage`
    emit("update:isSelect", true); // 🟢 Cập nhật trạng thái hiển thị chi tiết
};

onMounted(getPokemon);
</script>

<template>
    <div class="items"> 
        <PokemonItem 
            v-for="(pokemon, index) in displayedPokemons" 
            :key="index" 
            :pokemon="pokemon" 
            @handleSelectPokemon="handleSelectPokemon"
        />
    </div>
    <LoadMore v-if="displayedPokemons.length < filteredPokemons.length" @handleLoadMore="handleLoadMore"/>
</template>