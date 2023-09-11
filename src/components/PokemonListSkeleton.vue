<template>
  <div class="pokemon skeleton">
    <div class="sprite-container">
      <div class="sprite" alt="pokemon sprite" />
    </div>
    <span class="number" />
    <span class="name" />
    <div class="types-container">
      <div class="pokemon-types">
        <span v-for="i in 2" :key="i" :style="{ width: `${Math.floor(Math.random() * 2) + 8}rem` }" class="type" />
      </div>
    </div>
  </div>
</template>
