<template>
  <v-card height="500"
          elevation="2"
  >
    <v-card-title>Verlauf</v-card-title>
    <v-card-subtitle></v-card-subtitle>
    <v-card-text>
      <v-container fluid>
        <v-row>
          <v-select
            :items="items"
            label="Standard"
            v-model="selectedItem"
            v-on:change="reloadData"
          ></v-select>
        </v-row>
        <v-row>
          <div id="chart" style="width: 100%">
            <apexchart type="area" height="250" :options="chartOptions" :series="series"></apexchart>
          </div>
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
                v-on:change="reloadData"
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
                v-on:change="reloadData"
              ></v-date-picker>
            </v-menu>
          </v-col>
        </v-row>
      </v-container>
    </v-card-text>
  </v-card>
</template>

<script>
import * as VueApexCharts from 'vue-apexcharts'
// import ApexCharts from 'vue-apexcharts'
import axios from 'axios'
export default {
  name: 'ApexchartsSparkline',
  components: {
    apexchart: VueApexCharts
  },
  mounted () {
    this.dateFrom = new Date(Date.now() - 86400000).toISOString().substr(0, 10)
    this.dateTo = new Date().toISOString().substr(0, 10)
    this.reloadData()
  },
  data: () => ({
    menuFrom: false,
    menuTo: false,
    dateFrom: String,
    dateTo: String,
    value: '',
    items: ['tank1', 'tank2', 'all'],
    selectedItem: 'all',
    dataTmp: [],
    series: [{
      name: 'Wasserstand',
      data: [
      ]
    }],
    chartOptions: {
      chart: {
        type: 'area',
        height: 350,
        zoom: {
          enabled: false
        }
      },
      dataLabels: {
        enabled: false
      },
      stroke: {
        curve: 'smooth'
      },
      xaxis: {
        type: 'datetime'
      },
      yaxis: {
        opposite: true
      },
      legend: {
        horizontalAlign: 'left'
      },
      annotations: {
        xaxis: [
          {
            x: 'Oct 06 14:00',
            borderColor: '#00E396',
            label: {
              borderColor: '#00E396',
              style: {
                fontSize: '12px',
                color: '#fff',
                background: '#00E396'
              },
              orientation: 'horizontal',
              offsetY: 7,
              text: 'Annotation Test'
            }
          }
        ]
      }
    }
  }),
  methods: {
    reloadData: function () {
      this.dataTmp = []
      axios.get('http://localhost:8090/waterLevels/tank/' + this.selectedItem + '/valuesAndMillis?from=' + this.dateFrom + 'T00:00&to=' + this.dateTo + 'T23:59')
        .then(response => (response.data.forEach(x => this.dataTmp.push({ x: x.time, y: x.waterLevel }))))
        .then(_ => this.reDraw())
    },
    reDraw: function () {
      this.series = []
      this.series = [{
        name: 'Wasserstand',
        data: this.dataTmp
      }]
    }
  }
}
</script>

<style scoped>

</style>
