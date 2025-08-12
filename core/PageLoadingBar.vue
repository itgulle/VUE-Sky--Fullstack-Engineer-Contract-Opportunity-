<template>
  <div class="progress progress-sm position-sticky top-0 start-0 bg-transparent">
    <div
      ref="bar"
      class="progress-bar"
      role="progressbar"
      :class="{
        'progress-bar--finished': finishedLoading
      }"
      :style="{
        width: `${progress}%`
      }"
      :aria-valuenow="progress"
      aria-valuemin="0"
      aria-valuemax="100"
    ></div>
  </div>
</template>

<script>
import routeProgressBus from "../../routeProgressBus"

export default {
  name: "PageLoadingBar",
  data() {
    return {
      progress: 0,
      finishedLoading: false
    }
  },
  created() {
    let loadingTimeout
    routeProgressBus.$on("started loading route", () => {
      loadingTimeout = setInterval(() => {
        if (this.progress > 95) {
          this.progress += 0.02
        } else if (this.progress > 80) {
          this.progress += 0.1
        } else {
          this.progress += 0.5
        }
      }, 10)
    })
    routeProgressBus.$on("finished loading route", () => {
      clearInterval(loadingTimeout)
      this.progress = 100
      this.fadeOutBar()
    })
  },
  methods: {
    fadeOutBar() {
      this.finishedLoading = true
      setTimeout(() => {
        this.progress = 0
        this.finishedLoading = false
      }, 500)
    }
  }
}
</script>

<style lang="scss" scoped>
.progress {
  z-index: 1003;
}
.progress-bar {
  background: linear-gradient(90deg, rgba(235, 1, 74, 1) 0%, rgba(5, 4, 140, 1) 55%);
  opacity: 1;
  transition: opacity 0.25s linear;
  &--finished {
    opacity: 0;
  }
}
</style>
