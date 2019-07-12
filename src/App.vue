<template>
  <v-app>
    <v-content>
      <HomePage />
    </v-content>
  </v-app>
</template>

<script>
import HomePage from '@/pages/HomePage'

export default {
  name: 'App',
  components: {
    HomePage
  }
}
</script>
