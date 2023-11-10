<script setup>
import { typeInfos } from '~/modules/typeinfos'

const limit = 151
const offset = 0
const searchQuery = ref('')
const pokemonList = ref([])
const selectedPokemon = ref(null)
const closingPokemonDetails = ref(false)
const isMobileView = ref(window.innerWidth < 768)
const isLoadingDetail = ref(false)
const isLoadingList = ref(false)

function getLanguage(arr, lang = 'en') {
  return arr.find(item => item.language.name === lang)
}

function getIdFromUrl(url) {
  const parts = url.split('/')
  return Number.parseInt(parts[parts.length - 2])
}

async function fetchPokemonList() {
  isLoadingList.value = true
  const response = await fetch(`https://pokeapi.co/api/v2/pokemon?limit=${limit}&offset=${offset}`)
  const data = await response.json()
  const pokemonPromises = data.results.map(async (pokemon) => {
    return {
      id: getIdFromUrl(pokemon.url),
      pokedexNumber: getIdFromUrl(pokemon.url),
      name: pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1),
      sprite: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${getIdFromUrl(pokemon.url)}.png`,
      types: [],
    }
  })
  pokemonList.value = await Promise.all(pokemonPromises)

  if (!isMobileView.value) {
    // Select random by default
    const randomNumber = Math.floor(Math.random() * limit) + 1
    const defaultPokemon = pokemonList.value.find(pokemon => pokemon.id === randomNumber)
    if (defaultPokemon)
      fetchPokemonDetails(defaultPokemon)
  }
  getAllTypes()
}

async function fetchPokemonDetails(pokemon) {
  isLoadingDetail.value = true

  // Fetch details of the selected Pokemon
  const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${pokemon.id}`)
  const data = await response.json()

  // Fetch species
  const speciesResponse = await fetch(`https://pokeapi.co/api/v2/pokemon-species/${pokemon.id}`)
  const speciesData = await speciesResponse.json()

  // Fetch types
  const types = data.types.map(type => type.type.name)

  // Fetch evolution
  const responseEvolutions = await fetch(speciesData.evolution_chain.url)
  const evolutionChain = await responseEvolutions.json()

  function getEvolutionChain(evolution) {
    const chains = []

    function traverseEvolution(evolution) {
      if (!evolution)
        return

      const id = getIdFromUrl(evolution.species.url)
      const name = evolution.species.name
      const evolutionDetails = evolution.evolution_details[0]
      const minLevel = evolutionDetails?.min_level || ''
      const sprite = `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${getIdFromUrl(evolution.species.url)}.png`

      chains.push({ id, name, minLevel, sprite })

      if (evolution.evolves_to.length > 0) {
        evolution.evolves_to.forEach((evo) => {
          traverseEvolution(evo)
        })
      }
    }
    traverseEvolution(evolution)
    return chains
  }
  const evolution = getEvolutionChain(evolutionChain.chain)

  const abilitiesData = await Promise.all(
    data.abilities.map(async (ability) => {
      const response = await fetch(ability.ability.url)
      return response.json()
    }),
  )

  const abilities = abilitiesData.map((abilityData) => {
    const name = getLanguage(abilityData.names, 'en').name
    return name
  })

  const stats = data.stats.map((stat) => {
    return {
      name: stat.stat.name,
      value: stat.base_stat,
    }
  })

  selectedPokemon.value = {
    ...pokemon,
    pokedexNumber: pokemon.id,
    animatedSprite: data.sprites.versions['generation-v']['black-white'].animated.front_default
      || pokemon.sprite,
    height: data.height / 10,
    weight: data.weight / 10,
    description: getLanguage(speciesData.flavor_text_entries)?.flavor_text ?? '',
    species: getLanguage(speciesData.names).name,
    stats,
    category: getLanguage(speciesData.genera)?.genus,
    abilities,
    types,
    evolution,
  }
  isLoadingDetail.value = false
}

async function getAllTypes() {
  isLoadingList.value = true

  const response = await fetch('https://pokeapi.co/api/v2/type')
  const typesData = await response.json()

  const typePromises = typesData.results.map(async (type) => {
    const typeResponse = await fetch(type.url)
    const typeData = await typeResponse.json()

    const typeName = typeData.name
    const typePokemons = typeData.pokemon.map((pokemon) => {
      return {
        id: getIdFromUrl(pokemon.pokemon.url),
        name: pokemon.pokemon.name.charAt(0).toUpperCase() + pokemon.pokemon.name.slice(1),
      }
    })
    return { name: typeName, pokemons: typePokemons }
  })

  const typeList = await Promise.all(typePromises)

  for (const type of typeList) {
    for (const pokemon of pokemonList.value) {
      const matchingPokemon = type.pokemons.find(p => p.name === pokemon.name)
      if (matchingPokemon)
        pokemon.types.push(type.name)
    }
  }
  isLoadingList.value = false
}

// search logic
const filteredPokemonList = computed(() => {
  const query = searchQuery.value.toLowerCase()
  return pokemonList.value.filter(
    pokemon =>
      pokemon.name.toLowerCase().includes(query)
      || pokemon.pokedexNumber.toString().includes(query)
      || pokemon.types.some(type => type.toLowerCase().includes(query)),
  )
})

function selectPokemon(pokemon) {
  fetchPokemonDetails(pokemon)
  if (isMobileView.value)
    document.body.classList.add('scroll-lock')
  const pokemonDetails = document.querySelector('.pokemon-details')
  if (pokemonDetails && isMobileView.value)
    pokemonDetails.scrollTop = 0
}

function closePokemonDetails() {
  closingPokemonDetails.value = true
  if (isMobileView.value)
    document.body.classList.remove('scroll-lock')
  setTimeout(() => {
    if (closingPokemonDetails.value) {
      selectedPokemon.value = null
      closingPokemonDetails.value = false
    }
  }, 800)
}

function toggleBodyCursor() {
  const body = document.body
  isLoadingDetail.value ? body.classList.add('loading-cursor') : body.classList.remove('loading-cursor')
}

watch(isLoadingDetail, () => {
  toggleBodyCursor()
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
      <template v-if="isLoadingList">
        <pokemon-list-skeleton v-for="i in 15" :key="i" />
      </template>

      <template v-else>
        <div v-for="pokemon in filteredPokemonList" :key="pokemon.name" class="pokemon" @click="selectPokemon(pokemon)">
          <img class="sprite" :src="pokemon.sprite" alt="pokemon sprite" loading="lazy">
          <span class="number">N° {{ pokemon.pokedexNumber }}</span>
          <span class="name">{{ pokemon.name }}</span>
          <div class="types-container">
            <div class="pokemon-types">
              <span
                v-for="type in pokemon.types"
                :key="type"
                :style="{ background: typeInfos[type].color }"
                class="type"
              >
                {{ type }}
              </span>
            </div>
          </div>
        </div>
      </template>
    </div>

    <div v-if="isLoadingDetail && !selectedPokemon && !isMobileView" class="details-container">
      <pokemon-details-skeleton />
    </div>

    <div
      v-else-if="selectedPokemon"
      class="details-container"
      :class="{ 'slide-in': selectedPokemon, 'slide-out': closingPokemonDetails, 'loading-cursor': isLoadingDetail }"
    >
      <div v-if="isMobileView && selectedPokemon" :style="{ background: typeInfos[selectedPokemon.types[0]].color }" class="overlay" @click="closePokemonDetails" />
      <pokemon-details :pokemon="selectedPokemon" :type-infos="typeInfos" :select-pokemon="selectPokemon" />
      <div v-if="isMobileView" i-carbon-close-outline class="close-button" @click="closePokemonDetails" />
    </div>
  </main>
</template>
