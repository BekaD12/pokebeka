<script setup>
const { pokemon, typeColors } = defineProps(['pokemon', 'typeColors'])

const statTranslations = {
  'hp': 'PV',
  'attack': 'Attaque',
  'defense': 'Défense',
  'special-attack': 'Attaque Spéciale',
  'special-defense': 'Défense Spéciale',
  'speed': 'Vitesse',
}

function getTranslatedStatName(statName) {
  return statTranslations[statName] || statName
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
          <span class="stat-name">{{ getTranslatedStatName(stat.name) }}</span>
          <span class="stat-value">{{ stat.value }}</span>
        </div>
        <div class="total-stats">
          <span class="total-label">Total</span>
          <span class="total-value">{{ getTotalStats() }}</span>
        </div>
      </div>
    </div>

    <div class="evolution-container">
      <div class="pokemon-evolution">
        <h3>Evolution:</h3>
        <div v-for="evolutionStep in pokemon.evolution" :key="evolutionStep.speciesName">
          <img src="" alt="">
          <p v-if="evolutionStep.minLevel">
            Level {{ evolutionStep.minLevel }}
            {{ evolutionStep.speciesName }}
          </p>
        </div>
      </div>
    </div>
  </div>
</template>
