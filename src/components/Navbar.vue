<template>
  <v-toolbar
    app
    color="amber darken-4"
    dark
    flat
  >
    <v-toolbar-title class="headline">
      <span class="font-weight-light">Address</span>
      <span> Search</span>
    </v-toolbar-title>
    <v-autocomplete
      v-model="select"
      :loading="loadingAutocomplete"
      :items="suggestions"
      item-text="label"
      :search-input.sync="addressInput"
      cache-items
      color="brown darken-1"
      class="full-screen mx-3"
      flat
      hide-no-data
      hide-details
      label="What address do you want to check?"
      solo-inverted
      clearable
      return-object
    ></v-autocomplete>
  </v-toolbar>
</template>

<script>
import axios from 'axios'
export default {
  props: {
    appId: String,
    appCode: String
  },
  data() {
    return {
      loadingAutocomplete: false,
      error: null,
      suggestions: [],
      addressInput: null,
      select: null
    }
  },
  watch: {
    addressInput(address) {
      address && address !== this.select && this.searchAddress(address)
    },
    select(address) {
      address && this.chooseAddress(address)
    }
  },
  methods: {
    searchAddress(address) {
      this.loadingAutocomplete = true
      const data = {
        app_id: this.appId,
        app_code: this.appCode,
        query: address,
        maxresults: 10,
        matchLevel: 'houseNumber'
      }
      axios
        .get('http://autocomplete.geocoder.cit.api.here.com/6.2/suggest.json', {
          params: data
        })
        .then((response) => {
          if (response.data.suggestions) {
            this.suggestions = response.data.suggestions
          }
        })
        .catch((error) => {
          this.error = true
        })
        .finally(() => (this.loadingAutocomplete = false))
    },
    chooseAddress(address) {
      this.$emit('setMainLoading', true)
      const data = {
        app_id: this.appId,
        app_code: this.appCode,
        locationId: address.locationId
      }
      axios
        .get('http://geocoder.api.here.com/6.2/geocode.json', {
          params: data
        })
        .then((response) => {
          const addressData = response.data.Response.View[0].Result[0]
          this.$emit('setAddressData', addressData)
        })
        .finally(() => this.$emit('setMainLoading', false))
    }
  }
}
</script>