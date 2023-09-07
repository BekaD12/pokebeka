<template>
  <div class="pokemon-details">
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
        Il lui arrive de remettre d’aplomb
        un Pikachu allié en lui envoyant
        une décharge électrique.
      </p>
    </div>

    <div class="category-container">
      <span class="title">Categories</span>
      <div class="pokemon-category">
        <span class="category">Pokémon Souris</span>
      </div>
    </div>

    <div class="abilities-container">
      <span class="title">Compétences</span>
      <div class="pokemon-abilities">
        <span class="abilities">
          Statik
        </span>
        <span class="abilities">
          Paratonnerre
        </span>
      </div>
    </div>

    <div class="info-container">
      <div class="pokemon-info">
        <div class="size">
          <span class="title">Taille</span>
          <span class="height-value">0.4 Mètres</span>
        </div>
        <div class="weight">
          <span class="title">Poids</span>
          <span class="weight-value">6 Kilos</span>
        </div>
      </div>
    </div>

    <div class="stats-container">
      <span class="title">Statistiques</span>
      <div class="pokemon-stats">
        <div class="stats">
          <span class="stat-name" style="background: rgb(255, 0, 0);">pv</span><span class="stat-value">35</span>
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
      <div class="pokemon-evolution" />
    </div>
  </div>
</template>

<style scoped>
.pokemon-details{
    width: 500px;
}
</style>
