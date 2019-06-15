<template>
  <div>
    <InputField
      v-model="searchValue"
      v-bind="$attrs"
      ref="input"
      @input="openSuggestions"
      @change="onChange"
      @blur="closeSuggestions"
      @focus.stop="openSuggestions"
    ><slot></slot></InputField>
    <ul class="suggestions raised-2" v-show="showSuggestions">
      <li
        v-for="(suggestion, i) of filteredSuggestions"
        :key="i"
        @mousedown="selectSuggestion(suggestion)">
        {{ suggestion }}
      </li>
    </ul>
  </div>
</template>

<script>
import InputField from './InputField.vue'
export default {
  name: 'AutoComplete',

  components: {
    InputField,
  },

  props: {
    value: String,
    suggestions: Array,
  },

  data: function() {
    return {
      searchValue: this.value,
      showSuggestions: false,
    }
  },

  watch: {
    searchValue () {
      this.$refs.input.inputValue = this.searchValue
    },
  },

  computed:{
    filteredSuggestions: function() {
      const val = this.searchValue.toLowerCase()
      return val ? this.suggestions.filter(s => s.toLowerCase().indexOf(val) !== -1) : this.suggestions
    }
  },

  methods: {
    onChange: function(value) {
      if (this.filteredSuggestions.length === 1) {
        this.selectSuggestion(this.filteredSuggestions[0])
      } else {
        this.selectSuggestion(value)
      }
    },
    openSuggestions: function() {
      this.showSuggestions = true
    },
    closeSuggestions: function() {
      this.showSuggestions = false
    },
    selectSuggestion: function(suggestion) {
      this.searchValue = suggestion
      this.closeSuggestions()
      this.$emit('selected', suggestion)
    },
  },
}
</script>

<style scoped>
ul.suggestions {
  position: absolute;
  background: var(--theme-background, #fff);
  z-index: 9;
  min-width: 19rem;
  max-height: 16rem;
  overflow-y: scroll;
  list-style: none;
  margin: 0.5em 1em;
  padding: 0;
}
ul.suggestions li {
  padding: 0.75em 1em;
  cursor: pointer;
}
</style>