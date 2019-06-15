<template>
  <div id="app">
    <Header>
      <span>Aseman junatiedot</span>
    </Header>
    <div class="content">
      <LiveTrainInfo/>
    </div>
  </div>
</template>

<script>
import Header from './components/ui/Header.vue'
import LiveTrainInfo from './components/LiveTrainInfo.vue'

export default {
  name: 'app',

  components: {
    Header,
    LiveTrainInfo,
  },
}
</script>

<style>
.content {
  padding: 3rem;
}
</style>
