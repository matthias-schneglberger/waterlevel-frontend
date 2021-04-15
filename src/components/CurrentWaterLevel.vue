<template>
    <v-card height="500"
      elevation="2"
    >
      <v-card-title>Aktueller Wasserpegel</v-card-title>
      <v-card-subtitle></v-card-subtitle>
      <v-card-text class="justify-center">
        <v-container align="center" fluid class="justify-center">
          <v-row>
            <v-col cols="auto">
              <v-container >
                <v-row>
                  <v-col>
                      <v-progress-circular
                        :rotate="-90"
                        :size="150"
                        :width="25"
                        :value="percValueTank2"
                        color="primary"
                      >
                        {{ labelTank2 }} / 4000
                      </v-progress-circular>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col>
                      <v-progress-circular
                        :rotate="-90"
                        :size="150"
                        :width="25"
                        :value="percValueTank1"
                        color="primary"
                      >
                        {{ labelTank1 }} / 8000
                      </v-progress-circular>
                  </v-col>
                </v-row>
              </v-container>
            </v-col>
            <v-col cols="auto">
              <v-container>
                <v-progress-circular
                  :rotate="-90"
                  :size="325"
                  :width="50"
                  :value="percValueAll"
                  color="primary"
                >
                  {{ labelAll }} / 12000
                </v-progress-circular>
              </v-container>
            </v-col>
          </v-row>
        </v-container>
        <v-btn
          elevation="2"
          @click="reloadData"
        >Neu laden</v-btn>
      </v-card-text>
    </v-card>
</template>

<script>
import axios from 'axios'

export default {
  name: 'CurrentWaterLevel',
  mounted () {
    this.reloadData()
  },
  data: () => ({
    percValueTank1: 0,
    percValueTank2: 0,
    percValueAll: 0,
    labelTank1: 0,
    labelTank2: 0,
    labelAll: 0,
    lastResponse: ''

  }),
  methods: {
    reloadData: function () {
      this.percValueTank1 = 0
      this.percValueTank2 = 0
      this.percValueAll = 0
      this.labelTank1 = 0
      this.labelTank2 = 0
      this.labelAll = 0

      axios.get('http://localhost:8090/waterLevels/current', { responseType: 'json' }).then(response => (this.lastResponse = response.data)).then(_ => this.putDataInView())
    },

    putDataInView: function () {
      console.log(this.lastResponse)

      this.labelTank1 = this.lastResponse.tank1
      this.labelTank2 = this.lastResponse.tank2
      this.labelAll = this.lastResponse.all
      this.percValueTank1 = (this.lastResponse.tank1 / 4000) * 100
      this.percValueTank2 = (this.lastResponse.tank2 / 8000) * 100
      this.percValueAll = (this.lastResponse.all / 12000) * 100

      console.log(this.lastResponse)
    }
  }
}
</script>

<style scoped>

</style>
