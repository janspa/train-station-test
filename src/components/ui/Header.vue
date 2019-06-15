<template>
  <div class="header bg-primary">
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
    box-shadow: 0 0 10px 4px rgba(0, 0, 0, 0.5);
  }
</style>