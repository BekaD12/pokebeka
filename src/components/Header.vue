<script setup lang="ts">
import { availableLocales, loadLanguageAsync } from '~/modules/i18n'

const { t, locale } = useI18n()

async function toggleLocales() {
  // change to some real logic
  const locales = availableLocales
  const newLocale = locales[(locales.indexOf(locale.value) + 1) % locales.length]
  await loadLanguageAsync(newLocale)
  locale.value = newLocale
}
</script>

<template>
  <nav flex="~ gap-6" m-8 justify-between>
    <router-link class="navbar-logo" to="/" aria-label="Pokedex">
      <h1>
        <svg xmlns="http://www.w3.org/2000/svg" width="32" height="32" viewBox="0 0 48 48"><mask id="ipSPokeballOne0"><g fill="none"><path stroke="#fff" stroke-linejoin="round" stroke-width="4" d="M24 44c11.046 0 20-8.954 20-20S35.046 4 24 4S4 12.954 4 24s8.954 20 20 20Z" /><circle cx="24" cy="24" r="6" fill="#fff" stroke="#fff" stroke-linejoin="round" stroke-width="4" /><path stroke="#fff" stroke-linecap="round" stroke-linejoin="round" stroke-width="4" d="M30 24h14M4 24h14" /><circle cx="24" cy="24" r="2" fill="#000" /></g></mask><path fill="currentColor" d="M0 0h48v48H0z" mask="url(#ipSPokeballOne0)" /></svg>
        PokéBeka - Pokédex
      </h1>
    </router-link>
    <button icon-btn :title="t('button.toggle_dark')" @click="toggleDark()">
      <div i="carbon-sun dark:carbon-moon" />
    </button>

    <!-- <a icon-btn :title="t('button.toggle_langs')" @click="toggleLocales()">
      <div i-carbon-language />
    </a> -->
  </nav>
</template>
