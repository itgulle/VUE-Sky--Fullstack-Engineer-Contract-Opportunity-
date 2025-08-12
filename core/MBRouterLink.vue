<template>
  <router-link :class="disabled ? 'disabled' : ''" :to="to" :aria-disabled="disabled">
    <slot></slot>
  </router-link>
</template>

<script>
export default {
  props: {
    disabled: {
      type: Boolean,
      required: false,
      default: true
    },
    to: {
      type: [String, Location, Object],
      required: false,
      default: function () {
        return ""
      }
    }
  }
}
</script>

<style></style>
