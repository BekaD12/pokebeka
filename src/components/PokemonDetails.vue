<script setup>
const { pokemon, typeColors, pokemonList } = defineProps(['pokemon', 'typeColors', 'pokemonList'])

// Stat name translations
const statTranslations = {
  'hp': 'PV',
  'attack': 'Attaque',
  'defense': 'Défense',
  'special-attack': 'Attaque Spéciale',
  'special-defense': 'Défense Spéciale',
  'speed': 'Vitesse',
}

function getTranslatedStatName(statName) {
  return statTranslations[statName] || statName // Return the translated name if available, otherwise the original name
}

// Calculate total stats
function getTotalStats() {
  return pokemon.stats.reduce((total, stat) => total + stat.value, 0)
}

// Function to get the sprite URL for a given species name
function getEvolutionSprite(speciesName) {
  const foundPokemon = pokemonList.find(pokemon => pokemon.name === speciesName)
  if (foundPokemon && foundPokemon.sprites && foundPokemon.sprites.front_default)
    return foundPokemon.sprites.front_default

  return '' // Return an empty string if the species or sprites are not found in the pokemonList
}
</script>

<template>
  <div class="modal">
    <div class="pokemon-details">
      <div class="img-container">
        <img :src="pokemon.animatedSprite" alt="pokemon sprite">
      </div>
      <p>#{{ pokemon.pokedexNumber }}</p>
      <h2>{{ pokemon.species }}</h2>

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

      <p>Anglais :{{ pokemon.name }}</p>

      <span
        v-for="abilities in pokemon.abilities"
        :key="abilities.name"
      >
        {{ abilities }}
      </span>

      <p>Taille :{{ pokemon.height }} Mètres</p>
      <p>Poids :{{ pokemon.weight }} Kilos</p>
      <p>{{ pokemon.description }}</p>

      <!-- Display Pokémon Stats -->
      <div class="pokemon-stats">
        <h3>Stats:</h3>
        <div v-for="stat in pokemon.stats" :key="stat.name">
          <span class="stat-name">{{ getTranslatedStatName(stat.name) }}:</span>
          <span class="stat-value">{{ stat.value }}</span>
        </div>
        <div class="total-stats">
          <span class="total-label">Total:</span>
          <span class="total-value">{{ getTotalStats() }}</span>
        </div>
      </div>

      <!-- Display Pokémon Evolution -->
      <div class="pokemon-evolution">
        <h3>Evolution:</h3>
        <div v-for="evolutionStep in pokemon.evolution" :key="evolutionStep.speciesName">
          <img :src="getEvolutionSprite(evolutionStep.speciesName)" :alt="evolutionStep.speciesName">
          <p v-if="evolutionStep.minLevel">
            Level {{ evolutionStep.minLevel }}
          </p>
        </div>
      </div>

      <button @click="selectedPokemon = null">
        Fermer
      </button>
    </div>
  </div>
</template>
