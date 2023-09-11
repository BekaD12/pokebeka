<script setup>
import { typeInfos } from '~/modules/typeinfos'

function getRandomIterationCount() {
  return Math.floor(Math.random() * 2) + 1
}

function getRandomColor() {
  const typeNames = Object.keys(typeInfos)
  const randomIndex = Math.floor(Math.random() * typeNames.length)
  const randomType = typeNames[randomIndex]
  return typeInfos[randomType].color
}
</script>

<template>
  <div class="pokemon skeleton">
    <div class="sprite-container">
      <div class="sprite" alt="pokemon sprite" />
    </div>
    <span class="number" />
    <span class="name" />
    <div class="types-container">
      <div class="pokemon-types">
        <span v-for="i in getRandomIterationCount()" :key="i" :style="{ width: `${Math.floor(Math.random() * 2) + 8}rem`, backgroundColor: getRandomColor() }" class="type" />
      </div>
    </div>
  </div>
</template>
