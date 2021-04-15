<template>
  <v-card height="500"
          elevation="2"
  >
    <v-card-title>Verlauf</v-card-title>
    <v-card-subtitle></v-card-subtitle>
    <v-card-text class="justify-center">
      <div>
        <line-chart :datasrc="datacollection"></line-chart>
<!--        <button @click="fillData()">Randomize</button>-->
      </div>
    </v-card-text>
  </v-card>

</template>

<script>
import LineChart from 'vue-chartjs'

export default {
  name: 'chartjsdiagramgbdate',
  components: {
    LineChart
  },
  data () {
    return {
      datacollection: null
    }
  },
  mounted () {
    this.fillData()
  },
  methods: {
    fillData () {
      this.datacollection = {
        labels: [this.getRandomInt(), this.getRandomInt()],
        datasets: [
          {
            label: 'Data One',
            backgroundColor: '#f87979',
            data: [this.getRandomInt(), this.getRandomInt()]
          }, {
            label: 'Data Two',
            backgroundColor: '#f87979',
            data: [this.getRandomInt(), this.getRandomInt()]
          }
        ]
      }
    },
    getRandomInt () {
      return Math.floor(Math.random() * (50 - 5 + 1)) + 5
    }
  }
}
</script>

<style>
.small {
  max-width: 600px;
  margin:  150px auto;
}
</style>
