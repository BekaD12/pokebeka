<script setup>
import { onMounted, ref } from 'vue'

const pokemonList = ref([])
const selectedPokemon = ref(null)
const isLoading = ref(false)

const typeColors = {
  bug: '#729f3f',
  dark: '#707070',
  dragon: 'linear-gradient(180deg, #53a4cf 50%, #f16e57 50%)',
  electric: '#eed535',
  fairy: '#fdb9e9',
  fighting: '#d56723',
  fire: '#fd7d24',
  flying: 'linear-gradient(180deg, #3dc7ef 50%, #bdb9b8 50%)',
  ghost: '#7b62a3',
  grass: '#9dca5d',
  ground: 'linear-gradient(180deg, #f7de3f 50%, #ab9842 50%)',
  ice: '#51c4e7',
  normal: '#a4acaf',
  poison: '#b97fc9',
  psychic: '#f366b9',
  rock: '#a38c21',
  shadow: '#0e2e4c',
  steel: '#9eb7b8',
  water: '#4592c4',
}

function getLanguage(arr, lang = 'fr') {
  return arr.find(item => item.language.name === lang)
}

async function fetchPokemonList() {
  const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=1010')
  const data = await response.json()
  pokemonList.value = data.results.map(pokemon => ({
    pokedexNumber: getIdFromUrl(pokemon.url),
    id: getIdFromUrl(pokemon.url),
    name: pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1),
    sprite: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${getIdFromUrl(pokemon.url)}.png`, // Add the sprite URL here
  }))

  // Select random by default
  const randomNumber = Math.floor(Math.random() * (900 - 1 + 1)) + 1
  const defaultPokemon = pokemonList.value.find(pokemon => pokemon.id === randomNumber)
  if (defaultPokemon)
    fetchPokemonDetails(defaultPokemon)
}

async function fetchPokemonDetails(pokemon) {
  // isLoading.value = true // Set loading state to false after fetching data
  // Fetch species details first
  const speciesResponse = await fetch(`https://pokeapi.co/api/v2/pokemon-species/${pokemon.id}`)
  const speciesData = await speciesResponse.json()

  // Find the French description from species details
  const frenchDescription = getLanguage(speciesData.flavor_text_entries, 'fr')

  // Fetch the full details of the selected Pokemon
  const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${pokemon.id}`)
  const data = await response.json()

  const stats = data.stats.map((stat) => {
    return {
      name: stat.stat.name,
      value: stat.base_stat,
    }
  })

  const abilitiesData = await Promise.all(
    data.abilities.map(async (ability) => {
      const response = await fetch(ability.ability.url)
      return response.json()
    }),
  )
  const abilities = abilitiesData.map((abilityData) => {
    const name = getLanguage(abilityData.names, 'fr').name
    return name
  })

  // Fetch the types in French and English
  const typesData = await Promise.all(
    data.types.map(async (type) => {
      const response = await fetch(type.type.url)
      return response.json()
    }),
  )

  // Extract the French and English type names
  const types = typesData.map((typeData) => {
    const frenchName = getLanguage(typeData.names, 'fr').name
    const englishName = getLanguage(typeData.names, 'en').name
    return { french: frenchName, english: englishName }
  })

  const animatedSprite = data.sprites.versions['generation-v']['black-white'].animated.front_default
  const normalSprite = data.sprites.front_default

  selectedPokemon.value = {
    ...pokemon,
    animatedSprite: animatedSprite || normalSprite,
    pokedexNumber: data.id,
    height: data.height / 10,
    weight: data.weight / 10,
    description: frenchDescription ? frenchDescription.flavor_text : '',
    species: getLanguage(speciesData.names).name,
    stats,
    category: getLanguage(speciesData.genera)?.genus,
    abilities,
    types,
  }
}

function selectPokemon(pokemon) {
  fetchPokemonDetails(pokemon)
}

function getIdFromUrl(url) {
  const parts = url.split('/')
  return Number.parseInt(parts[parts.length - 2])
}

const searchQuery = ref('')

const filteredPokemonList = computed(() => {
  const query = searchQuery.value.toLowerCase()
  return pokemonList.value.filter(
    pokemon =>
      pokemon.name.toLowerCase().includes(query)
      || pokemon.pokedexNumber.toString().includes(query),
    // || pokemon.types.some(type => type.french.toLowerCase().includes(query)),
  )
})

onMounted(() => {
  fetchPokemonList()
})
</script>

<template>
  <div class="search-container">
    <input v-model="searchQuery" type="search" placeholder="Search Pokémon">
  </div>

  <main class="pokemon-container">
    <div class="pokemon-grid">
      <div v-for="pokemon in filteredPokemonList" :key="pokemon.name" class="pokemon" @click="selectPokemon(pokemon)">
        <img class="sprite" :src="pokemon.sprite" alt="pokemon sprite">
        <span class="name">{{ pokemon.name }}</span>
        <span class="number">No. {{ pokemon.pokedexNumber }}</span>

        <div class="types-container">
          <div class="pokemon-types">
            <span
              v-for="type in pokemon.types" :key="type.french"
              :style="{ background: typeColors[type.english.toLowerCase()] }"
              class="type"
            >
              {{ type.french }}
            </span>
          </div>
        </div>
      </div>
    </div>

    <div v-if="isLoading" class="details-container">
      <pokemon-details-skeleton :pokemon="selectedPokemon" :type-colors="typeColors" />
    </div>

    <div v-if="selectedPokemon" class="details-container">
      <pokemon-details :pokemon="selectedPokemon" :type-colors="typeColors" />
    </div>
  </main>
</template>
