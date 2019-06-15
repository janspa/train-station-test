<template>
  <div class="input-field-wrapper">
    <slot></slot>
    <input
      v-bind="$attrs"
      :value="value"
      @input="$emit('input', $event.target.value)"
      @change="$emit('change', $event.target.value)"
    >
  </div>
</template>

<script>
export default {
  name: 'InputField',

  inheritAttrs: false,

  props: {
    value: String,
  },
}
</script>

<style scoped>
.input-field-wrapper label {
  font-weight: bold;
  display: block;
  margin-bottom: 0.25em;
}
.input-field-wrapper input {
  width: 20rem;
}
</style>