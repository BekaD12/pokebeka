<template>
  <div class="pokemon-details skeleton">
    <div class="img-container">
      <img
        class="sprite" src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/versions/generation-v/black-white/25.png" alt="pokemon sprite"
      >
    </div>
    <span class="number">#25</span>
    <span class="name">Pikachu</span>

    <div class="types-container">
      <div class="pokemon-types">
        <span class="type" style="background: rgb(238, 213, 53);">
          Électrik
        </span>
      </div>
    </div>

    <div class="description-container">
      <p class="description">
        When several of these POKéMON gather, their electricity could build and cause lightning storms.
      </p>
    </div>

    <div class="category-container">
      <span class="title">Categories</span>
      <div class="pokemon-category">
        <span class="category">Mouse Pokémon</span>
      </div>
    </div>

    <div class="abilities-container">
      <span class="title">Abilities</span>
      <div class="pokemon-abilities">
        <span class="abilitie">
          Static
        </span>
        <span class="abilitie">
          Lightning Rod
        </span>
      </div>
    </div>

    <div class="info-container">
      <div class="pokemon-info">
        <div class="size">
          <span class="title">Height</span>
          <span class="height-value">0.4 Meters</span>
        </div>
        <div class="weight">
          <span class="title">Weight</span>
          <span class="weight-value">6 Kilos</span>
        </div>
      </div>
    </div>

    <div class="stats-container">
      <span class="title">Stats</span>
      <div class="pokemon-stats">
        <div class="stats">
          <span class="stat-name" style="background: rgb(255, 0, 0);">hp</span><span class="stat-value">35</span>
        </div><div class="stats">
          <span class="stat-name" style="background: rgb(240, 128, 48);">atk</span><span class="stat-value">55</span>
        </div><div class="stats">
          <span class="stat-name" style="background: rgb(248, 208, 48);">def</span><span class="stat-value">40</span>
        </div><div class="stats">
          <span class="stat-name" style="background: rgb(104, 144, 240);">spa</span><span class="stat-value">50</span>
        </div><div class="stats">
          <span class="stat-name" style="background: rgb(120, 200, 80);">spd</span><span class="stat-value">50</span>
        </div><div class="stats">
          <span class="stat-name" style="background: rgb(248, 88, 136);">spd</span><span class="stat-value">90</span>
        </div><div class="total-stats">
          <span class="total-label">TOT</span><span class="total-value">320</span>
        </div>
      </div>
    </div>

    <div class="evolution-container">
      <div class="evolution-content">
        <div class="evolution-stage">
          <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/172.png" alt="evolution sprite" class="evolution-sprite">
        </div>
        <div class="evolution-stage">
          <span class="min-level">?</span>
          <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/25.png" alt="evolution sprite" class="evolution-sprite">
        </div><div class="evolution-stage">
          <span class="min-level">?</span>
          <img src="https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/26.png" alt="evolution sprite" class="evolution-sprite">
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.pokemon-details{
    width: 500px;
}
</style>
