<template>
  <div v-show="isActive" class="tab">
    <slot />
  </div>
</template>

<script>
export default {
  name: 'Tab',

  props: {
    label: String
  },

  data: () => ({
    isActive: false,
  })
}
</script>