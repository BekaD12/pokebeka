<script setup lang="ts">
// https://github.com/vueuse/head
// you can use this to manipulate the document head in any components,
// they will be rendered correctly in the html results with vite-ssg
useHead({
  title: 'Pokébeka',
  meta: [
    { name: 'description', content: 'Pokébeka - Pokedex' },
    {
      name: 'theme-color',
      content: () => isDark.value ? '#14181D' : '#edf1F5',
    },
  ],
  link: [
    {
      rel: 'icon',
      type: 'image/svg+xml',
      href: () => preferredDark.value ? '/favicon-dark.svg' : '/favicon.svg',
    },
  ],
})
</script>

<template>
  <RouterView />
</template>
