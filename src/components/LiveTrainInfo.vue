<template>
  <div>
    <div v-if="loading">
      Odota hetki...
    </div>
    <div v-else>
      <md-autocomplete
        v-model="selectedStation"
        :md-options="stationNames"
        @md-selected="handleStationSelect">
        <label>Hae aseman nimellä</label>
      </md-autocomplete>
    </div>
  </div>
</template>

<script>
import Vue from 'vue'
import { MdAutocomplete } from 'vue-material/dist/components'

Vue.use(MdAutocomplete)

const API_URL = "https://rata.digitraffic.fi/api/v1/"
const apiLiveTrainOpts = {
  arrived_trains: 0,
  departed_trains: 0,
  arriving_trains: 10,
  departing_trains: 10,
  include_nonstopping: true,
}

export default {
  name: 'LiveTrainInfo',
  data: () => ({
    loading: true,
    selectedStation: '',
    stations: [],
    trainsArriving: [],
    trainsDeparting: [],
  }),
  computed: {
    stationNames: function () {
      return this.stations.map(s => s.stationName)
    },
  },
  created: async function() {
    this.loading = true
    this.stations = await this.fetchStations()
    this.loading = false
  },
  methods: {
    fetchStations() {
      const url = new URL(`metadata/stations`, API_URL)
      return fetch(url)
        .then(res => res.json())
        .then(data => data
          .filter(stations => stations.passengerTraffic)
          .map(station => {
            // remove the extraneous ' asema' suffix
            if (/ asema$/.test(station.stationName)) {
              station.stationName = station.stationName.slice(0, 7)
            }
            // convert underscores to spaces
            station.stationName = station.stationName.replace(/_/g, ' ')
            return station
          }))
    },
    fetchLiveTrains(stationCode) {
      const url = new URL(`live-trains/station/${stationCode}`, API_URL)
      for (let [key, value] of Object.entries(apiLiveTrainOpts)) {
        url.searchParams.append(key, value)
      }
      return fetch(url)
      .then(res => res.json())
      .then(data => {
        console.log(data)
      })
    },
    getStationByName(name) {
      return this.stations.find(s => s.stationName === name)
    },
    getStationByCode(code) {
      return this.stations.find(s => s.stationShortCode === code)
    },
    handleStationSelect() {
      const station = this.getStationByName(this.selectedStation)
      console.log(station)
      if (station) {
        this.fetchLiveTrains(station.stationShortCode)
      }
    },
  },
}
</script>