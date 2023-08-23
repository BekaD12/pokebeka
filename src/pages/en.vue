<script setup>

</script>

<template>
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
          <div class="pokemon-types" />
        </div>
      </div>
    </div>

    <div v-if="selectedPokemon" class="details-container">
      <pokemon-details :pokemon="selectedPokemon" :type-colors="typeColors" />
    </div>
  </main>
</template>
