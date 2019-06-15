<template>
  <div class="header bg-primary raised-1">
    <slot></slot>
  </div>
</template>

<script>
  export default {
    name: 'Header',
  }
</script>

<style>
  .header {
    font-size: 1.25em;
    padding: 1rem 3rem;
  }
</style>