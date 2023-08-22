<script setup>
const { pokemon, typeColors } = defineProps(['pokemon', 'typeColors'])

const statData = {
  'hp': { translation: 'pv', color: '#ff0000' },
  'attack': { translation: 'atk', color: '#f08030' },
  'defense': { translation: 'def', color: '#f8d030' },
  'special-attack': { translation: 'spa', color: '#6890f0' },
  'special-defense': { translation: 'spd', color: '#78c850' },
  'speed': { translation: 'spd', color: '#f85888' },
}

function getTranslatedStatName(statName) {
  return statData[statName]?.translation || statName
}

function getStatColor(statName) {
  return statData[statName]?.color || '#000000'
}

function getTotalStats() {
  return pokemon.stats.reduce((total, stat) => total + stat.value, 0)
}
</script>

<template>
  <div class="pokemon-details">
    <div class="img-container">
      <img class="sprite" :src="pokemon.animatedSprite" alt="pokemon sprite">
    </div>
    <span class="number">#{{ pokemon.pokedexNumber }}</span>
    <span class="name">{{ pokemon.species }} - {{ pokemon.name }}</span>

    <div class="types-container">
      <div class="pokemon-types">
        <span
          v-for="type in pokemon.types"
          :key="type.french"
          :style="{ background: typeColors[type.english.toLowerCase()] }"
          class="type"
        >
          {{ type.french }}
        </span>
      </div>
    </div>

    <div class="description-container">
      <p class="description">
        {{ pokemon.description }}
      </p>
    </div>

    <div class="category-container">
      <span class="title">Categories</span>
      <div class="pokemon-category">
        <span class="category">{{ pokemon.category }}</span>
      </div>
    </div>

    <div class="abilities-container">
      <span class="title">Compétences</span>
      <div class="pokemon-abilities">
        <span
          v-for="abilities in pokemon.abilities"
          :key="abilities.name"
          class="abilities"
        >
          {{ abilities }}
        </span>
      </div>
    </div>

    <div class="info-container">
      <div class="pokemon-info">
        <div class="size">
          <span class="title">Taille</span>
          <span class="height-value">{{ pokemon.height }} Mètres</span>
        </div>
        <div class="weight">
          <span class="title">Poids</span>
          <span class="weight-value">{{ pokemon.weight }} Kilos</span>
        </div>
      </div>
    </div>

    <div class="stats-container">
      <span class="title">Statistiques</span>
      <div class="pokemon-stats">
        <div v-for="stat in pokemon.stats" :key="stat.name" class="stats">
          <span :style="{ background: getStatColor(stat.name) }" class="stat-name">{{ getTranslatedStatName(stat.name) }}</span>
          <span class="stat-value">{{ stat.value }}</span>
        </div>
        <div class="total-stats">
          <span class="total-label">TOT</span>
          <span class="total-value">{{ getTotalStats() }}</span>
        </div>
      </div>
    </div>

    <div class="evolution-container">
      <div class="pokemon-evolution" />
    </div>
  </div>
</template>
