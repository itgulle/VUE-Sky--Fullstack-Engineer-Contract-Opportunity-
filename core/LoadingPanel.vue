<template>
  <div ref="loadingPanel" :style="loadingPanelHeight">
    <div v-if="showSpinner" class="d-flex gap-3 align-items-center">
      <div class="text-primary fs-2">Loading...</div>
      <div class="spinner-border text-primary"></div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { onMounted, ref, useTemplateRef } from "vue"

const loadingPanelHeight = ref("height: 50px")
const showSpinner = ref(false)
const loadingPanelRef = useTemplateRef<HTMLDivElement>("loadingPanel")

const setloadingPanelHeight = () => {
  const panel = loadingPanelRef.value
  const rect = panel.getBoundingClientRect()
  const panelHeight = window.innerHeight - rect.bottom - 65
  loadingPanelHeight.value = `height: ${panelHeight}px`
}

const showLoadingSpinner = () => {
  setTimeout(() => {
    showSpinner.value = true
  }, 300)
}

onMounted(() => {
  setloadingPanelHeight()
  showLoadingSpinner()
})
</script>

<style lang="css" scoped>
.spinner-border {
  width: 1.9rem;
  height: 1.9rem;
  border-width: 0.2rem;
}
</style>
