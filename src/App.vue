<template>
  <div id="app">
    <md-toolbar class="md-primary">
      <header class="md-title">Aseman junatiedot</header>
    </md-toolbar>
    <div class="content">
      <LiveTrainInfo/>
    </div>
  </div>
</template>

<script>
import LiveTrainInfo from './components/LiveTrainInfo.vue'

export default {
  name: 'app',
  components: {
    LiveTrainInfo
  },
}
</script>

<style>
#app {
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
}
.md-toolbar {
  padding: 1rem 3rem;
}
.content {
  padding: 1rem 3rem;
}
</style>
