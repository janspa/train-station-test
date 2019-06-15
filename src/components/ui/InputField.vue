<template>
  <div class="input-field">
    <slot></slot>
    <div class="input-wrapper">
      <input
        v-bind="$attrs"
        ref="input"
        v-model="inputValue"
        :value="value"
        @input="$emit('input', inputValue)"
        @change="$emit('change', inputValue)"
        @focus="onFocus($event)"
        @blur="onBlur($event)"
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
    focused: false,
  }),

  methods: {
    clearInput: function() {
      this.inputValue = ''
      this.$emit('input', this.inputValue)
      this.$emit('change', this.inputValue)
    },
    onFocus: function(event) {
      this.focused = true
      this.$emit('focus', event)
    },
    onBlur: function(event) {
      this.focused = false
      this.$emit('blur', event)
    },
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