<script setup>
import { onMounted, ref } from 'vue'

const searchQuery = ref('')
const pokemonList = ref([])
const selectedPokemon = ref(null)

const typeColors = {
  normal: '#bcbcac',
  fighting: '#bc5442',
  flying: '#669aff',
  poison: '#ab549a',
  ground: '#debc54',
  rock: '#bcac66',
  bug: '#abbc1c',
  ghost: '#6666bc',
  steel: '#abacbc',
  fire: '#ff421c',
  water: '#2f9aff',
  grass: '#78cd54',
  electric: '#ffcd30',
  psychic: '#ff549a',
  ice: '#78deff',
  dragon: '#7866ef',
  dark: '#785442',
  fairy: '#ffacff',
  shadow: '#0e2e4c',
}

async function fetchPokemonData(pokemon) {
  const response = await fetch(pokemon.url)
  const data = await response.json()

  // Fetch the species data for French display
  const speciesResponse = await fetch(data.species.url)
  const speciesData = await speciesResponse.json()

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

  // Fetch abilities
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

  // Fetch stats
  const stats = data.stats.map((stat) => {
    return {
      name: stat.stat.name,
      value: stat.base_stat,
    }
  })

  // Fetch evolution data if available
  let evolution = null
  if (speciesData.evolution_chain) {
    const evolutionChainResponse = await fetch(speciesData.evolution_chain.url)
    const evolutionChainData = await evolutionChainResponse.json()
    evolution = extractEvolutionData(evolutionChainData)
  }

  return {
    pokedexNumber: data.id,
    name: data.name,
    sprite: data.sprites.front_default,
    animatedSprite: data.sprites.versions['generation-v']['black-white'].animated.front_default,
    species: getLanguage(speciesData.names).name,
    description: getLanguage(speciesData.flavor_text_entries, 'fr').flavor_text,
    types,
    height: data.height,
    weight: data.weight,
    abilities,
    stats,
    evolution,
  }
}

function extractEvolutionData(evolutionChainData) {
  const evolutionData = []

  const traverseEvolutionChain = (chain) => {
    if (!chain)
      return

    const speciesName = chain.species.name
    const minLevel = chain.evolution_details[0]?.min_level || null

    evolutionData.push({ speciesName, minLevel })

    // Handle branching evolution paths if available
    if (chain.evolves_to.length > 1) {
      chain.evolves_to.forEach((branch) => {
        traverseEvolutionChain(branch)
      })
    }
    else {
      traverseEvolutionChain(chain.evolves_to[0])
    }
  }

  traverseEvolutionChain(evolutionChainData.chain)

  return evolutionData
}

const filteredPokemonList = computed(() => {
  const query = searchQuery.value.trim().toLowerCase()
  if (!query)
    return pokemonList.value

  return pokemonList.value.filter((pokemon) => {
    const nameMatch = pokemon.species.toLowerCase().includes(query)
    const typeMatch = pokemon.types.some(
      type => type.french.toLowerCase().includes(query) || type.english.toLowerCase().includes(query),
    )
    const numberMatch = pokemon.pokedexNumber.toString().includes(query)

    return nameMatch || typeMatch || numberMatch
  })
})

function showPokemonDetails(pokemon) {
  selectedPokemon.value = pokemon
}

function getLanguage(arr, lang = 'fr') {
  return arr.find(item => item.language.name === lang)
}

onMounted(async () => {
  const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=151')
  const data = await response.json()
  const mappedData = await Promise.all(data.results.map(fetchPokemonData))
  pokemonList.value = mappedData
})
</script>

<template>
  <input
    v-model="searchQuery"
    type="text"
    placeholder="Search Pokémon"
  >

  <main>
    <div class="pokemon-grid">
      <div v-for="pokemon in filteredPokemonList" :key="pokemon.name" class="pokemon" @click="showPokemonDetails(pokemon)">
        <img
          class="sprite"
          :src="pokemon.sprite"
          alt="pokemon sprite"
        >
        <span class="name">{{ pokemon.species }} / {{ pokemon.name }}</span>
        <span class="number">Pokédex Number: #{{ pokemon.pokedexNumber }}</span>

        <div class="pokemon-types">
          <span
            v-for="type in pokemon.types"
            :key="type.french"
            :style="{ backgroundColor: typeColors[type.english.toLowerCase()] }"
            class="type"
          >
            {{ type.french }}
          </span>
        </div>
      </div>
    </div>

    <div v-if="selectedPokemon" class="modal-container">
      <pokemon-details :pokemon="selectedPokemon" :type-colors="typeColors" :pokemon-list="pokemonList" />
    </div>
  </main>
</template>
