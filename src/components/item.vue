<template>
  <li class="item" :class="bg" :style="{backgroundColor: item.color}">
    <slot></slot>
  </li>
</template>

<script>
  export default {
    name: 'item',
    computed: {
      bg() {
        return {
          transparent: !this.item.cross
        }
      }
    },
    props: {
      item: {
        type: Object,
        required: true
      }
    }
  };
</script>

<style>
  .transparent {
    background-color: transparent !important;
  }
</style>
