<script setup>
const searchQuery = ref('')
const pokemonList = ref([])
const selectedPokemon = ref(null)
const closingPokemonDetails = ref(false)
const isMobileView = ref(window.innerWidth < 768)
const isLoading = ref(false)
const typeInfos = {
  bug: { fr: 'Insecte', color: '#729f3f' },
  dark: { fr: 'Ténèbres', color: '#707070' },
  dragon: { fr: 'Dragon', color: 'linear-gradient(180deg, #53a4cf 50%, #f16e57 50%)' },
  electric: { fr: 'Électrik', color: '#eed535' },
  fairy: { fr: 'Fée', color: '#fdb9e9' },
  fighting: { fr: 'Combat', color: '#d56723' },
  fire: { fr: 'Feu', color: '#fd7d24' },
  flying: { fr: 'Vol', color: 'linear-gradient(180deg, #3dc7ef 50%, #bdb9b8 50%)' },
  ghost: { fr: 'Spectre', color: '#7b62a3' },
  grass: { fr: 'Plante', color: '#9dca5d' },
  ground: { fr: 'Sol', color: 'linear-gradient(180deg, #f7de3f 50%, #ab9842 50%)' },
  ice: { fr: 'Glace', color: '#51c4e7' },
  normal: { fr: 'Normal', color: '#a4acaf' },
  poison: { fr: 'Poison', color: '#b97fc9' },
  psychic: { fr: 'Psy', color: '#f366b9' },
  rock: { fr: 'Roche', color: '#a38c21' },
  shadow: { fr: 'Ombre', color: '#0e2e4c' },
  steel: { fr: 'Acier', color: '#9eb7b8' },
  water: { fr: 'Eau', color: '#4592c4' },
}

function getLanguage(arr, lang = 'fr') {
  return arr.find(item => item.language.name === lang)
}

function getIdFromUrl(url) {
  const parts = url.split('/')
  return Number.parseInt(parts[parts.length - 2])
}

async function fetchPokemonList() {
  const response = await fetch('https://pokeapi.co/api/v2/pokemon?limit=1010')
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
    const randomNumber = Math.floor(Math.random() * 1010) + 1
    const defaultPokemon = pokemonList.value.find(pokemon => pokemon.id === randomNumber)
    if (defaultPokemon)
      fetchPokemonDetails(defaultPokemon)
  }
  getAllTypes()
}

async function fetchPokemonDetails(pokemon) {
  if (!isMobileView.value)
    isLoading.value = true

  // Fetch details of the selected Pokemon
  const response = await fetch(`https://pokeapi.co/api/v2/pokemon/${pokemon.id}`)
  const data = await response.json()

  // Fetch species
  const speciesResponse = await fetch(`https://pokeapi.co/api/v2/pokemon-species/${pokemon.id}`)
  const speciesData = await speciesResponse.json()

  // Fetch evolution
  const responseEvolutions = await fetch(speciesData.evolution_chain.url)
  const evolutionChain = await responseEvolutions.json()

  function getEvolutionChain(evolution) {
    const chains = []

    function traverseEvolution(evolution) {
      if (!evolution)
        return

      const name = evolution.species.name
      const evolutionDetails = evolution.evolution_details[0]
      const minLevel = evolutionDetails?.min_level || ''
      const sprite = `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${getIdFromUrl(evolution.species.url)}.png`

      chains.push({ name, minLevel, sprite })

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
    const name = getLanguage(abilityData.names, 'fr').name
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
    types: pokemon.types,
    evolution,
  }
  isLoading.value = false
}

async function getAllTypes() {
  const typeList = []
  for (let i = 0; i < 18; i++) {
    const url = `https://pokeapi.co/api/v2/type/${i + 1}`
    const response = await fetch(url)
    const typeData = await response.json()

    const typeName = typeData.name
    const typePokemons = typeData.pokemon.map((pokemon) => {
      return {
        id: getIdFromUrl(pokemon.pokemon.url),
        name: pokemon.pokemon.name.charAt(0).toUpperCase() + pokemon.pokemon.name.slice(1),
      }
    })
    typeList.push({ name: typeName, pokemons: typePokemons })
  }

  // Update the types property of each Pokémon in pokemonList
  for (const type of typeList) {
    for (const pokemon of pokemonList.value) {
      const matchingPokemon = type.pokemons.find(p => p.name === pokemon.name)
      if (matchingPokemon)
        pokemon.types.push(type.name)
    }
  }
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
        <img class="sprite" :src="pokemon.sprite" alt="pokemon sprite" loading="lazy">
        <span class="number">N° {{ pokemon.pokedexNumber }}</span>
        <span class="name">{{ pokemon.name }}</span>
        <div class="types-container">
          <div class="pokemon-types">
            <span v-for="type in pokemon.types" :key="type" :style="{ background: typeInfos[type].color }" class="type">
              {{ typeInfos[type].fr }}</span>
          </div>
        </div>
      </div>
    </div>

    <div v-if="isLoading" class="details-container">
      <pokemon-details-skeleton />
    </div>

    <div
      v-if="selectedPokemon" class="details-container"
      :class="{ 'slide-in': selectedPokemon, 'slide-out': closingPokemonDetails }"
    >
      <div
        v-if="isMobileView && selectedPokemon" :style="{ background: typeInfos[selectedPokemon.types[0]].color }"
        class="overlay" @click="closePokemonDetails"
      />
      <french-details :pokemon="selectedPokemon" :type-infos="typeInfos" />
      <div v-if="isMobileView" i-carbon-close-outline class="close-button" @click="closePokemonDetails" />
    </div>
  </main>
</template>
