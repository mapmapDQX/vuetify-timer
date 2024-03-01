<template>
  <v-app>
    <v-main>
      <Timer />
    </v-main>
  </v-app>
</template>

<script setup>
  import { useTheme } from 'vuetify'
  const theme = useTheme()
  theme.global.name.value = 'dark'
</script>
