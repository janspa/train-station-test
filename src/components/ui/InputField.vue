<template>
  <div class="input-field">
    <slot></slot>
    <div class="input-wrapper">
      <input
        v-bind="$attrs"
        v-model="inputValue"
        :value="value"
        @input="$emit('input', $event.target.value)"
        @change="$emit('change', $event.target.value)"
      >
      <button
        class="clear-button"
        v-show="inputValue"
        @click="clearInput"
      >&times;</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'InputField',

  inheritAttrs: false,

  props: {
    value: String,
  },

  data: () => ({
    inputValue: '',
  }),

  methods: {
    clearInput: function() {
      this.inputValue = ''
    }
  }
}
</script>

<style scoped>
.input-wrapper {
  position: relative;
  display: inline;
}
.input-field label {
  font-weight: bold;
  display: block;
  margin-bottom: 0.25em;
}
.input-field input {
  width: 20rem;
}
.clear-button {
  position: absolute;
  top: 0;
  right: 0;
  height: 100%;
  width: 2em;
  z-index: 9;
  border: 0;
  padding: 0;
  background: transparent;
  cursor: pointer;
}
</style>