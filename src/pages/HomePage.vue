<template>
  <div class="app-content">
    <navbar
      :appId="appId"
      :appCode="appCode"
      @setAddressData="setAddressData"
      @setMainLoading="setMainLoading"
    />
    <v-container fluid>
      <v-layout wrap>
        <v-flex
          xs12
          sm7
          md8
        >
          <map-display
            :platform="platform"
            :addressCoords="addressCoords"
            :places="places"
            :radius="radius"
            :mainLoading="mainLoading"
            @showPlaceInfo="showPlaceInfo"
          />
        </v-flex>
        <v-flex
          xs12
          sm5
          md4
        >
          <v-fade-transition mode="out-in">
            <keep-alive>
              <search-places-form
                v-if="!showPlaceCard"
                :addressCoords="addressCoords"
                :platform="platform"
                @setPlacesData="setPlacesData"
                @setMainLoading="setMainLoading"
              />
              <place-card
                v-else
                :placeInfo="placeInfo"
                @closePlaceCard="closePlaceCard"
              />
            </keep-alive>
          </v-fade-transition>
        </v-flex>
      </v-layout>
    </v-container>
  </div>
</template>

<script>
import Navbar from '@/components/Navbar.vue'
import MapDisplay from '@/components/MapDisplay.vue'
import SearchPlacesForm from '@/components/SearchPlacesForm.vue'
import PlaceCard from '@/components/PlaceCard.vue'
export default {
  components: {
    Navbar,
    MapDisplay,
    SearchPlacesForm,
    PlaceCard
  },
  data() {
    return {
      appId: 'h1ndQOi21kvnqXfr04Ze',
      appCode: 'vyYwdwsu3o1u2sjcRRs1sw',
      platform: {},
      addressCoords: {
        lat: null,
        lng: null
      },
      places: [],
      radius: null,
      showPlaceCard: false,
      placeInfo: {},
      mainLoading: false
    }
  },
  methods: {
    setAddressData(e) {
      this.addressData = e
      const {
        Location: {
          DisplayPosition: { Latitude: lat, Longitude: lng }
        }
      } = this.addressData
      this.addressCoords.lat = lat
      this.addressCoords.lng = lng
    },
    setPlacesData(e, radius) {
      this.places = e
      this.radius = radius
    },
    showPlaceInfo(e) {
      this.showPlaceCard = true
      this.placeInfo = e
    },
    closePlaceCard() {
      this.showPlaceCard = false
    },
    setMainLoading(e) {
      this.loadingMain = e
    }
  },
  created() {
    this.platform = new H.service.Platform({
      app_id: this.appId,
      app_code: this.appCode,
      useHTTPS: true,
      useCIT: true
    })
  }
}
</script>

<style scoped>
.app-content,
.container,
.layout {
  height: 100%;
}
</style>