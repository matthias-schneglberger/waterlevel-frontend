<template>
  <v-card height="500"
    elevation="2"
  >
    <v-card-title>Verlauf</v-card-title>
    <v-card-subtitle></v-card-subtitle>
    <v-card-text class="justify-center">
      <v-container fluid>
        <v-row>
          <v-select
            :items="items"
            label="Standard"
            v-model="selectedItem"
            v-on:change="reloadLine"
          ></v-select>
        </v-row>
        <v-row>
          <v-sparkline
            :fill="fill"
            :gradient="primary"
            :line-width="width"
            :padding="padding"
            :smooth="radius || false"
            :value="value"
            auto-draw
          ></v-sparkline>
        </v-row>
      </v-container>
      <v-container>
        <v-row>
          <v-col cols="3">
            <v-menu
              v-model="menuFrom"
              :close-on-content-click="false"
              :nudge-right="40"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field
                  v-model="dateFrom"
                  label="Filter: von"
                  prepend-icon="mdi-calendar"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                ></v-text-field>
              </template>
              <v-date-picker
                v-model="dateFrom"
                @input="menuFrom = false"
                v-on:change="reloadLine"
              ></v-date-picker>
            </v-menu>
          </v-col>
          <v-col cols="3">
            <v-menu
              v-model="menuTo"
              :close-on-content-click="false"
              :nudge-right="40"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field
                  v-model="dateTo"
                  label="Filter: bis"
                  prepend-icon="mdi-calendar"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                ></v-text-field>
              </template>
              <v-date-picker
                v-model="dateTo"
                @input="menuTo = false"
                v-on:change="reloadLine"
              ></v-date-picker>
            </v-menu>
          </v-col>
        </v-row>
      </v-container>
      <v-btn
        elevation="2"
        @click="reloadLine"
      >Neu laden</v-btn>
    </v-card-text>
  </v-card>
</template>

<script>
import axios from 'axios'
export default {
  name: 'SparklineWaterlevel',
  mounted () {
    this.dateFrom = new Date(Date.now() - 86400000).toISOString().substr(0, 10)
    this.dateTo = new Date().toISOString().substr(0, 10)
    this.reloadLine()
  },
  data: () => ({
    menuFrom: false,
    menuTo: false,
    fill: false,
    padding: 8,
    radius: 10,
    value: [0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0],
    width: 2,
    dateFrom: String,
    dateTo: String,
    items: ['tank1', 'tank2', 'all'],
    selectedItem: 'all'
  }),
  methods: {
    reloadLine: function () {
      axios.get('http://localhost:8090/waterLevels/tank/' + this.selectedItem + '/values?from=' + this.dateFrom + 'T00:00&to=' + this.dateTo + 'T23:59').then(response => (this.value = response.data))
    }
  }
}
</script>

<style scoped>

</style>
