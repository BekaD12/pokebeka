<script setup>
const searchQuery = ref('')
const pokemonList = ref([])
const selectedPokemon = ref(null)

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

// Search
const filteredPokemonList = computed(() => {
  const query = searchQuery.value.toLowerCase()
  return pokemonList.value.filter(
    pokemon =>
      pokemon.name.toLowerCase().includes(query)
      || pokemon.pokedexNumber.toString().includes(query)
      || pokemon.types.some(type => type.french.toLowerCase().includes(query)),
  )
})

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

  return {
    pokedexNumber: data.id,
    name: getLanguage(speciesData.names, 'en').name,
    sprite: data.sprites.front_default,
    animatedSprite: data.sprites.versions['generation-v']['black-white'].animated.front_default,
    species: getLanguage(speciesData.names).name,
    description: getLanguage(speciesData.flavor_text_entries, 'fr').flavor_text,
    types,
    height: data.height / 10,
    weight: data.weight / 10,
    abilities,
    stats,
    category: getLanguage(speciesData.genera)?.genus,
  }
}

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
  selectedPokemon.value = pokemonList.value.find(pokemon => pokemon.pokedexNumber === 25) || null
})
</script>

<template>
  <div class="search-container">
    <input v-model="searchQuery" type="search" placeholder="Search Pokémon">
  </div>

  <main class="pokemon-container">
    <div class="pokemon-grid">
      <div
        v-for="pokemon in filteredPokemonList" :key="pokemon.name" class="pokemon"
        @click="showPokemonDetails(pokemon)"
      >
        <img class="sprite" :src="pokemon.sprite" alt="pokemon sprite">
        <span class="name">{{ pokemon.species }}</span>
        <span class="number">No. {{ pokemon.pokedexNumber }}</span>

        <div class="types-container">
          <div class="pokemon-types">
            <span
              v-for="type in pokemon.types" :key="type.french"
              :style="{ background: typeColors[type.english.toLowerCase()] }" class="type"
            >
              {{ type.french }}
            </span>
          </div>
        </div>
      </div>
    </div>

    <div v-if="selectedPokemon" class="details-container">
      <pokemon-details :pokemon="selectedPokemon" :type-colors="typeColors" />
    </div>
  </main>
</template>
