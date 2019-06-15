<template>
  <div>
    <div v-if="loading">
      Odota hetki...
    </div>
    <div v-else>
        <!--<md-autocomplete
          v-model="selectedStationName"
          :md-options="stationNames"
          @md-selected="handleStationSelect">
          <label>Hae aseman nimellä</label>
        </md-autocomplete>-->
      <InputField
        v-on:change="handleStationSelect">
        <label>Hae aseman nimellä</label>
      </InputField>
      <Tabs style="margin-top:2rem">
        <Tab label="Saapuvat">
          <LiveTrainTable :trains="trains" :stations="stations" :selectedStation="selectedStation" timeTableType="ARRIVAL" />
        </Tab>
        <Tab label="Lähtevät">
          <LiveTrainTable :trains="trains" :stations="stations" :selectedStation="selectedStation" timeTableType="DEPARTURE" />
        </Tab>
      </Tabs>
    </div>
  </div>
</template>

<script>
import InputField from './ui/InputField.vue'
import Tabs from './ui/Tabs.vue'
import Tab from './ui/Tab.vue'
import LiveTrainTable from './LiveTrainTable.vue'

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

  components: {
    Tabs,
    Tab,
    InputField,
    LiveTrainTable,
  },

  data: () => ({
    loading: true,
    selectedStation: null,
    stations: [],
    trains: [],
  }),

  computed: {
    passengerStations: function() {
      return this.stations.filter(station => station.passengerTraffic)
    },
    stationNames: function () {
      return this.passengerStations.map(s => s.stationName)
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
          .map(station => {
            // remove the extraneous ' asema' suffix
            if (/ asema$/.test(station.stationName)) {
              station.stationName = station.stationName.slice(0, -6)
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
        this.trains = data
      })
    },
    getStationByName(name) {
      return this.stations.find(s => s.stationName.toLowerCase() === name.toLowerCase())
    },
    getStationByCode(code) {
      return this.stations.find(s => s.stationShortCode.toLowerCase() === code.toLowerCase())
    },
    handleStationSelect(name) {
      this.selectedStation = null
      const station = this.getStationByName(name)
      if (station) {
        this.selectedStation = station
        this.fetchLiveTrains(station.stationShortCode)
      }
    },
  },
}
</script>