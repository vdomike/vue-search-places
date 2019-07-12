<template>
  <div
    class="map"
    ref="map"
  >
    <div
      class="loader"
      v-show="mainLoading"
    >
      <v-progress-circular
        :size="70"
        :width="7"
        color="amber darken-3"
        indeterminate
      ></v-progress-circular>
    </div>
  </div>
</template>

<script>
import { iconPlace } from '@/assets/icons/icons'
export default {
  data() {
    return {
      map: {},
      placesGroup: {},
      circle: {}
    }
  },
  props: {
    platform: Object,
    addressCoords: Object,
    places: Array,
    radius: Number,
    mainLoading: Boolean
  },
  mounted() {
    this.map = new H.Map(
      this.$refs.map,
      this.platform.createDefaultLayers().normal.map,
      {
        zoom: 14,
        center: { lat: 55.75222, lng: 37.61556 }
      }
    )
    const behavior = new H.mapevents.Behavior(
      new H.mapevents.MapEvents(this.map)
    )
    this.placesGroup = new H.map.Group()
  },
  watch: {
    addressCoords: {
      handler({ lat, lng }) {
        this.setLocationMarker(lat, lng)
      },
      deep: true
    },
    radius(val) {
      this.setCircle(val)
    },
    places(val) {
      this.setPlaceMarkers(val)
    }
  },
  methods: {
    setLocationMarker(lat, lng) {
      const icon = new H.map.DomIcon(iconPlace)
      this.map.removeObjects(this.map.getObjects())
      this.map.setCenter({ lat, lng })
      this.map.addObject(new H.map.DomMarker({ lat, lng }, { icon }))
    },
    setCircle(radius) {
      const { lat, lng } = this.addressCoords
      if (this.circle instanceof H.map.Circle) {
        this.map.removeObject(this.circle)
      }
      this.circle = new H.map.Circle({ lat, lng }, radius, {
        style: {
          strokeColor: '#ff6f00',
          fillColor: 'rgba(255, 111, 0, 0.3)'
        }
      })
      this.map.addObject(this.circle)
    },
    setPlaceMarkers(places) {
      this.placesGroup.removeAll()
      if (places.length > 0) {
        let zIndex = 1
        places.forEach((place) => {
          const [lat, lng] = place.position
          const icon = new H.map.Icon(place.icon)
          const placeObject = new H.map.Marker({ lat, lng }, { icon })
          placeObject.setData(place)
          placeObject.addEventListener('tap', (e) => {
            if (e.target instanceof mapsjs.map.Marker) {
              e.target.setZIndex(zIndex++)
              this.$emit('showPlaceInfo', e.target.getData())
            }
          })
          this.placesGroup.addObject(placeObject)
        })
        this.map.addObject(this.placesGroup)
      }
    }
  }
}
</script>

<style scoped>
.map {
  width: 100%;
  height: 100%;
  position: relative;
}
.loader {
  width: 100%;
  height: 100%;
  position: absolute;
  background: rgba(255, 255, 255, 0.4);
  z-index: 10;
}
.v-progress-circular {
  position: relative;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
@media screen and (max-width: 600px) {
  .map {
    height: 50vh;
  }
}
</style>