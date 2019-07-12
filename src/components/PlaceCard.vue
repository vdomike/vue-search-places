<template>
  <v-card class="ml-4">
    <v-toolbar
      color="white"
      flat
      dense
    >
      <v-toolbar-title class="subheading grey--text text-uppercase">{{ placeInfo.category.title }}</v-toolbar-title>
      <v-spacer />
      <v-btn
        flat
        icon
        color="amber darken-4"
        @click="$emit('closePlaceCard')"
      >
        <v-icon>clear</v-icon>
      </v-btn>
    </v-toolbar>

    <v-card-title class="px-4">
      <div>
        <div class="display-1">{{ placeInfo.title }}</div>
        <div
          class="brown--text darken-1--text mt-2"
          d-flex
        >
          <v-icon class="brown--text darken-1--text">pin_drop</v-icon>
          {{ placeInfo.distance }}
          meters
          from
          location
        </div>
      </div>
    </v-card-title>

    <v-card-text class="px-4">
      <div
        class="mb-4"
        v-if="placeInfo.openingHours"
      >
        <span class="font-weight-medium">Opening hours</span>
        <div v-html="placeInfo.openingHours.text">
        </div>
      </div>
      <v-layout
        row
        wrap
        v-if="placeInfo.tags"
      >
        <v-chip
          color="amber darken-2"
          text-color="white"
          v-for="tag in placeInfo.tags"
          :key="tag.group+tag.id"
        >
          {{ tag.group }}-{{ tag.id }}
        </v-chip>
      </v-layout>
    </v-card-text>
  </v-card>
</template>

<script>
export default {
  props: {
    placeInfo: Object
  }
}
</script>

<style scoped>
</style>