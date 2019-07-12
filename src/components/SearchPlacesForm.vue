<template>
  <v-form @submit.prevent="searchPlaces">
    <v-container class="pr-0">
      <v-layout>
        <v-flex xs12>
          <v-slider
            min="300"
            max="3000"
            step="300"
            label="Search radius (meters)"
            v-model="radius"
            color="brown darken-1"
            thumb-color="red lighten-1"
            thumb-label="always"
          ></v-slider>
        </v-flex>
      </v-layout>
      <div
        v-if="nothingFound"
        class="red--text darken-1--text"
      >Nothing was found on this query</div>
      <div
        v-if="error"
        class="red--text darken-1--text"
      >{{ error }}</div>
      <v-layout
        column
        align-content-start
      >
        <v-flex
          xs12
          v-for="category in categories"
          :key=category.id
        >
          <v-expansion-panel>
            <v-expansion-panel-content hide-actions>
              <template v-slot:header>
                <v-layout
                  align-center
                  row
                  spacer
                >
                  <v-flex xs3>
                    <v-avatar
                      size="36px"
                      :color="category.iconColor"
                    >
                      <v-icon
                        dark
                        small
                      >{{ category.icon }}</v-icon>
                    </v-avatar>
                  </v-flex>
                  <v-flex xs9>
                    <strong v-html="category.title"></strong>
                  </v-flex>
                </v-layout>
              </template>
              <v-card>
                <v-list>
                  <template v-for="subcategory in category.subcategories">
                    <v-list-tile
                      :key="subcategory.title"
                      class="pl-4"
                    >
                      <v-checkbox
                        v-model="chosenCategories"
                        :color="category.iconColor"
                        :label="subcategory.title"
                        :value="subcategory.query"
                      ></v-checkbox>
                    </v-list-tile>
                  </template>
                </v-list>
              </v-card>
            </v-expansion-panel-content>
          </v-expansion-panel>
        </v-flex>
        <v-flex xs4>
          <v-btn
            block
            dark
            class="mt-4"
            color="red lighten-1"
            @click="searchPlaces"
          >Search</v-btn>
        </v-flex>
      </v-layout>
    </v-container>
  </v-form>
</template>

<script>
export default {
  data() {
    return {
      categories: [
        {
          id: 100,
          title: 'Cafes and Restaraunts',
          icon: 'restaurant',
          iconColor: 'amber lighten-1',
          subcategories: [
            {
              title: 'Restaurant',
              query: 'restaurant'
            },
            {
              title: 'Coffee and Tea',
              query: 'coffee-tea'
            }
          ]
        },
        {
          id: 300,
          title: 'Sights and Museums',
          icon: 'account_balance',
          iconColor: 'amber darken-3',
          subcategories: [
            {
              title: 'Tourist Attraction',
              query: 'landmark-attraction'
            },
            {
              title: 'Museum',
              query: 'museum'
            },
            {
              title: 'Religious Place',
              query: 'religious-place'
            }
          ]
        },
        {
          id: 600,
          title: 'Shopping',
          icon: 'local_grocery_store',
          iconColor: 'amber darken-4',
          subcategories: [
            {
              title: 'Mall-Shopping Complex',
              query: 'mall-shopping-complex'
            },
            {
              title: 'Electronics',
              query: 'electronics'
            },
            {
              title: 'Bookstore',
              query: 'bookstore'
            },
            {
              title: 'Drugstore or Pharmacy',
              query: 'drugstore-or-pharmacy'
            },
            {
              title: 'Convenience Store',
              query: 'convenience-store'
            }
          ]
        }
      ],
      explore: {},
      radius: 600,
      chosenCategories: [],
      error: null,
      nothingFound: false
    }
  },
  props: {
    platform: Object,
    addressCoords: Object
  },
  mounted() {
    this.explore = new H.places.Explore(this.platform.getPlacesService())
  },
  methods: {
    searchPlaces() {
      if (!this.chosenCategories.length) {
        this.error = 'You should choose one or several categories'
      } else {
        this.$emit('setMainLoading', true)
        this.nothingFound = false
        this.error = null
        const areaToSearch = `${this.addressCoords.lat},${this.addressCoords.lng};r=${this.radius}`
        this.explore.request(
          {
            cat: this.chosenCategories.join(),
            in: areaToSearch,
            size: 30
          },
          {},
          (data) => {
            const {
              results: { items }
            } = data
            if (!items.length) {
              this.nothingFound = true
            }
            this.$emit('setPlacesData', items, this.radius)
            this.$emit('setMainLoading', false)
          },
          () => (this.error = 'Something went wrong. Please try a bit later')
        )
      }
    }
  }
}
</script>