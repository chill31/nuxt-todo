<script setup>
const colorMode = useColorMode()

const isDark = computed({
  get () {
    return colorMode.value === 'dark'
  },
  set () {
    colorMode.preference = colorMode.value === 'dark' ? 'light' : 'dark'
  }
})
</script>

<template>
  <ClientOnly>
    <UButton
      :icon="isDark ? 'i-heroicons-moon-20-solid' : 'i-heroicons-sun-20-solid'"
      variant="ghost"
      aria-label="Theme"
      @click="isDark = !isDark"
      class="absolute top-4 right-4 dark:text-white text-black dark:bg-primary-400 dark:hover:bg-primary-500 bg-primary-400 hover:bg-primary-300 rounded-full"
    />

    <template #fallback>
      <div class="w-8 h-8" />
    </template>
  </ClientOnly>
</template>
