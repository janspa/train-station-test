<template>
  <div>
    <div v-if="loadingStations">
      Odota hetki...
    </div>
    <div v-else>
      <AutoComplete
        v-model="selectedStationName"
        @selected="onStationSelect"
        :suggestions="stationNames"
        >
        <label>Hae aseman nimellä</label>
      </AutoComplete>
      <Tabs class="tabs">
        <Tab label="Saapuvat">
          <LiveTrainTable
            :loading="loadingTrains"
            :trains="trains"
            :stations="stations"
            :selectedStation="selectedStation"
            timeTableType="ARRIVAL" />
        </Tab>
        <Tab label="Lähtevät">
          <LiveTrainTable
            :loading="loadingTrains"
            :trains="trains"
            :stations="stations"
            :selectedStation="selectedStation"
            timeTableType="DEPARTURE" />
        </Tab>
      </Tabs>
    </div>
  </div>
</template>

<script>
import AutoComplete from './ui/AutoComplete.vue'
import Tabs from './ui/Tabs.vue'
import Tab from './ui/Tab.vue'
import LiveTrainTable from './LiveTrainTable.vue'

const API_URL = 'https://rata.digitraffic.fi/api/v1/'
const apiLiveTrainParams = {
  arrived_trains: 0,
  departed_trains: 0,
  arriving_trains: 10,
  departing_trains: 10,
  include_nonstopping: true,
}

export default {
  name: 'LiveTrainInfo',

  components: {
    Tabs,
    Tab,
    AutoComplete,
    LiveTrainTable,
  },

  data: () => ({
    loadingStations: true,
    loadingTrains: false,
    selectedStation: null,
    selectedStationName: '',
    stations: [],
    trains: [],
  }),

  watch: {
    selectedStationName () {
      this.onStationSelect(this.selectedStationName)
    },
  },

  computed: {
    passengerStations: function() {
      return this.stations.filter(station => station.passengerTraffic)
    },
    stationNames: function () {
      return this.passengerStations.map(s => s.stationName)
    },
  },

  created: async function() {
    this.stations = await this.fetchStations()
  },

  methods: {
    fetchStations() {
      this.loadingStations = true
      const url = new URL(`metadata/stations`, API_URL)
      return fetch(url)
        .then(res => res.json())
        .then(data => data
          .map(station => {
            // remove the extraneous ' asema' suffix
            if (/ asema$/.test(station.stationName)) {
              station.stationName = station.stationName.slice(0, -6)
            }
            // convert underscores to spaces
            station.stationName = station.stationName.replace(/_/g, ' ')
            return station
          }))
        .then(data => {
          this.loadingStations = false
          return data
        })
    },
    fetchLiveTrains(stationCode) {
      this.loadingTrains = true
      const url = new URL(`live-trains/station/${stationCode}`, API_URL)
      for (let [key, value] of Object.entries(apiLiveTrainParams)) {
        url.searchParams.append(key, value)
      }
      return fetch(url)
      .then(res => res.json())
      .then(data => {
        this.loadingTrains = false
        return data
      })
    },
    getStationByName(name) {
      return this.stations.find(s => s.stationName.toLowerCase() === name.toLowerCase())
    },
    getStationByCode(code) {
      return this.stations.find(s => s.stationShortCode.toLowerCase() === code.toLowerCase())
    },
    async onStationSelect(name) {
      const station = this.getStationByName(name)
      if (station) {
        this.selectedStation = station
        this.trains = await this.fetchLiveTrains(station.stationShortCode)
      } else {
        this.selectedStation = null
      }
    },
  },
}
</script>

<style scoped>
  .tabs {
    margin-top: 2rem;
    max-width: 600px;
  }
</style>