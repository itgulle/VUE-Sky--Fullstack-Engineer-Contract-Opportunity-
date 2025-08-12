<template>
  <div v-if="showOverlay" class="spinner-overlay">
    <div v-if="showSpinner" class="spinner-background">
      <div class="spinner-border text-primary"></div>
    </div>
    <div v-if="showText" class="spinner-text-background">
      <div class="spinner-text fs-3 text-primary">Please wait, almost done...</div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex"

export default {
  data() {
    return {
      showOverlay: false,
      showSpinner: false,
      showText: false,
      showTimeoutIds: [],
      hideTimeoutId: null
    }
  },
  computed: {
    ...mapGetters({
      shown: "layout/showWaitSpinner",
      silent: "layout/waitSpinnerSilent"
    })
  },
  watch: {
    shown(value) {
      if (value) {
        this.show()
      } else {
        this.hide()
      }
    }
  },
  methods: {
    show() {
      this.showOverlay = true

      if (this.silent) return

      clearTimeout(this.hideTimeoutId)

      const showSpinnerTimeoutId = setTimeout(() => {
        this.showSpinner = true
      }, 1000)
      this.showTimeoutIds.push(showSpinnerTimeoutId)

      const showTextTimeoutId = setTimeout(() => {
        this.showText = true
      }, 3000)
      this.showTimeoutIds.push(showTextTimeoutId)
    },
    hide() {
      //We don't to hide the spinner/text yet in case another spinner is used
      this.hideTimeoutId = setTimeout(() => {
        this.showTimeoutIds.forEach((id) => {
          clearTimeout(id)
        })
        this.showTimeoutIds.splice(0)
        this.showSpinner = false
        this.showText = false
      }, 500)

      //We do want to hide the overlay immediately
      this.showOverlay = false
    }
  }
}
</script>

<style scoped>
.spinner-overlay {
  pointer-events: visible;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 100000;
}

.spinner-background {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  background: #ffffff8c;
}

.spinner-border {
  position: relative;
  width: 7rem;
  height: 7rem;
  border-width: 0.5rem;
  z-index: 100002;
}

.spinner-text-background {
  position: absolute;
  display: flex;
  justify-content: center;
  align-items: center;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: #ffffff3d;
}

.spinner-text {
  position: relative;
  margin-top: 13rem;
  z-index: 100001;
}
</style>
