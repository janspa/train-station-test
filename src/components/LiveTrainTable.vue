<template>
  <div v-if="loading">
    Ladataan...
  </div>
  <div v-else-if="!selectedStation">
    Valitse asema.
  </div>
  <table v-else>
    <thead>
      <tr>
        <td>Juna</td>
        <td>Lähtöasema</td>
        <td>Pääteasema</td>
        <td>{{ timeTableType === 'ARRIVAL' ? 'Saapuu' : 'Lähtee' }}</td>
      </tr>
    </thead>
    <tbody>
      <tr
        v-for="(row, i) in timeTableRows"
        :key="i"
        :class="{ 'text-muted': isCancelled(row.train) }">
        <td>{{ row.train.trainType }} {{ row.train.trainNumber }}</td>
        <td>{{ getFirstStation(row.train) }}</td>
        <td>{{ getFinalStation(row.train) }}</td>
        <td v-if="isCancelled(row.train)">
          <div>{{ getScheduledTime(row) }}</div>
          <div class="text-danger">Cancelled</div>
        </td>
        <td v-else-if="isLate(row)">
          <div class="text-danger">{{ getEstimateTime(row) }}</div>
          <div class="small">({{ getScheduledTime(row) }})</div>
        </td>
        <td v-else>{{ getEstimateTime(row) }}</td>
      </tr>
    </tbody>
  </table>
</template>

<script>
  import { format } from 'date-fns'

  export default {
    name: 'LiveTrainTable',

    props: {
      loading: Boolean,
      trains: Array,
      stations: Array,
      selectedStation: Object,
      timeTableType: String,
    },

    computed: {
      timeTableRows: function() {
        return this.trains.map(train => {
          const row = this.getTimeTableRow(train)
          if (row) {
            row.train = train
          }
          return row
        })
        .filter(r => r)
        .sort((a, b) => (new Date(a.scheduledTime).getTime()) - (new Date(b.scheduledTime).getTime()))
      }
    },

    methods: {
      getTimeTableRow: function(train) {
        return train.timeTableRows.find(row => row.stationShortCode === this.selectedStation.stationShortCode && row.type === this.timeTableType)
      },
      getStationNameByCode: function(timeTableRow) {
        const code = timeTableRow.stationShortCode
        const station = this.stations.find(s => s.stationShortCode.toLowerCase() === code.toLowerCase())
        return station ? station.stationName : ''
      },
      getFirstStation: function(train) {
        return this.getStationNameByCode(train.timeTableRows[0])
      },
      getFinalStation: function(train) {
        return this.getStationNameByCode(train.timeTableRows[train.timeTableRows.length - 1])
      },
      formatTime: function(time) {
        return format(new Date(time), 'HH:mm')
      },
      getScheduledTime: function(row) {
        return this.formatTime(row.scheduledTime)
      },
      getEstimateTime: function(row) {
        return this.formatTime(row.liveEstimateTime || row.scheduledTime)
      },
      isCancelled: function(train) {
        return train.cancelled
      },
      isLate: function(row) {
        return row.differenceInMinutes != null && row.differenceInMinutes > 0
      },
    }
  }
</script>

<style scoped>
  table {
    width: 100%;
  }
  td {
    min-width: 25%;
  }
</style>