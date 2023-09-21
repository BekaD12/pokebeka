<script setup>
const { pokemon, typeInfos } = defineProps(['pokemon', 'typeInfos'])

const statData = {
  'hp': { short: 'hp', color: '#ff0000' },
  'attack': { short: 'atk', color: '#f08030' },
  'defense': { short: 'def', color: '#f8d030' },
  'special-attack': { short: 'spa', color: '#6890f0' },
  'special-defense': { short: 'spd', color: '#78c850' },
  'speed': { short: 'spd', color: '#f85888' },
}

const shortenedStat = computed(() => (statName) => {
  return statData[statName]?.short || statName
})
const statColor = computed(() => (statName) => {
  return statData[statName]?.color || '#000000'
})
const totalStats = computed(() => {
  return pokemon.stats.reduce((total, stat) => total + stat.value, 0)
})
</script>

<template>
  <div class="pokemon-details">
    <div class="img-container">
      <img class="sprite" :src="pokemon.animatedSprite" alt="pokemon sprite">
    </div>
    <span class="number">#{{ pokemon.pokedexNumber }}</span>
    <span class="name">{{ pokemon.name }}</span>
    <span class="category">{{ pokemon.category }}</span>

    <div class="types-container">
      <div class="pokemon-types">
        <span
          v-for="type in pokemon.types" :key="type"
          :style="{ background: typeInfos[type].color }"
          class="type"
        >
          {{ type }}
        </span>
      </div>
    </div>

    <div class="description-container">
      <p class="description">
        {{ pokemon.description }}
      </p>
    </div>

    <div class="abilities-container">
      <span class="title">Abilities</span>
      <div class="pokemon-abilities">
        <span v-for="abilities in pokemon.abilities" :key="abilities.name" class="abilitie">
          {{ abilities }}
        </span>
      </div>
    </div>

    <div class="info-container">
      <div class="pokemon-info">
        <div class="size">
          <span class="title">Height</span>
          <span class="height-value">{{ pokemon.height }} Meters</span>
        </div>
        <div class="weight">
          <span class="title">Weight</span>
          <span class="weight-value">{{ pokemon.weight }} Kilos</span>
        </div>
      </div>
    </div>

    <div class="stats-container">
      <span class="title">Stats</span>
      <div class="pokemon-stats">
        <div v-for="stat in pokemon.stats" :key="stat.name" class="stats">
          <span :style="{ background: statColor(stat.name) }" class="stat-name">{{ shortenedStat(stat.name)
          }}</span>
          <span class="stat-value">{{ stat.value }}</span>
        </div>
        <div class="total-stats">
          <span class="total-label">TOT</span>
          <span class="total-value">{{ totalStats }}</span>
        </div>
      </div>
    </div>

    <div class="evolution-container">
      <span v-show="pokemon.evolution.length > 1" class="title">Evolution</span>
      <div class="evolution-content">
        <div v-for="evolution, index in pokemon.evolution" :key="index" class="evolution-stage">
          <span v-if="evolution.minLevel" class="min-level">Lv. {{ evolution.minLevel }}</span>
          <span v-if="index > 0 && evolution.minLevel === ''" class="min-level">?</span>
          <div v-show="pokemon.evolution.length > 1" class="evolution-sprite-container">
            <img :src="evolution.sprite" alt="evolution sprite" class="evolution-sprite">
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
