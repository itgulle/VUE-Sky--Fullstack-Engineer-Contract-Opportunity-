<template>
  <button class="dropdown-item" @click="onClick">
    <slot></slot>
  </button>
</template>

<script setup lang="ts">
const emit = defineEmits<{ click: [] }>()

const onClick = () => emit("click")
</script>
