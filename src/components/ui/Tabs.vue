<template>
  <div class="tabs">
    <ul class="nav-tabs">
      <li
        v-for="(tab, i) in tabs"
        :key="i"
        :class="{ 'active': tab.isActive }"
        class="nav-item"
      >
        <a
          v-html="tab.label"
          @click="selectTab(tab, $event)"
          class="nav-link"
          href="#"
        ></a>
      </li>
    </ul>
    <div class="tabs-content">
      <slot />
    </div>
  </div>
</template>

<script>
export default {
  name: 'Tabs',

  data: () => ({
    tabs: [],
    currentTab: null,
  }),

  mounted: function() {
    this.tabs = this.$children
    if (this.tabs.length > 0) {
      this.selectTab(this.tabs[0])
    }
  },

  methods: {
    selectTab: function(selectedTab) {
      this.currentTab = selectedTab
      for (let tab of this.tabs) {
        tab.isActive = tab === selectedTab
      }
    }
  },
}
</script>

<style scoped>
  .nav-tabs {
    display: flex;
    list-style: none;
    padding: 0;
    margin: 0 0 1rem;
    border-bottom: 2px solid var(--theme-border, #cdd);
  }
  .nav-tabs .nav-item a {
    display: block;
    padding: 0.5em 1.5em;
  }
  .nav-tabs .nav-item {
    border: 2px solid transparent;
    border-top-left-radius: 0.5rem;
    border-top-right-radius: 0.5rem;
    margin-bottom: -2px;
  }
  .nav-tabs .nav-item:hover {
    border-color: var(--theme-border, #cdd);
  }
  .nav-tabs .nav-item.active {
    border-color: var(--theme-border, #cdd);
    border-bottom-color: var(--theme-background, #fff);
  }
  .nav-tabs .nav-item.active a {
    color: var(--theme-color);
  }
</style>