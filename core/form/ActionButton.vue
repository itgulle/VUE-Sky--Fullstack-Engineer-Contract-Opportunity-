<template>
  <div
    :class="{ disabled: waiting || isDisabled }"
    :disabled="waiting || isDisabled"
    @click.prevent="click()"
  >
    <div v-if="showOverlay" class="overlay">
      <div v-if="showSpinner" class="spinner-background">
        <div class="spinner-border text-primary"></div>
      </div>
    </div>
    <slot></slot>
  </div>
</template>

<script>
import { actionEmitter } from "../../../main"

export default {
  props: {
    disabled: {
      type: [Boolean, String],
      default: () => false
    },
    /**
     * If true then the button's hide function will only trigger if called manually through refs.
     * Useful if there are a lot of chained API calls that could take some time.
     */
    manualHide: {
      type: Boolean,
      default: false
    },
    /**
     * The action to perform. This action must return a Promise.
     */
    action: {
      type: Function,
      default: undefined //This will be required in the future
    },
    /**
     * Call back function to call once the process has finished.
     * The call back must accept a Promise as a parameter.
     */
    onClick: {
      type: Function,
      default: undefined //This is optional
    },
    /**
     * The time to wait before showing the waiting spinner in milliseconds.
     */
    spinnerDelayTime: {
      type: Number,
      default: () => 1500
    }
  },
  data() {
    return {
      showOverlay: false,
      showSpinner: false,
      showSpinnerTimeoutId: null,
      hideSpinnerTimeoutId: null,
      waiting: false,
      actionCallBack: null
    }
  },
  computed: {
    isDisabled() {
      return Boolean(this.disabled)
    }
  },
  mounted() {
    /**
     * TODO: Small delay added to reduce the likelihood of the button being reenabled too soon https://mortgageengine.atlassian.net/browse/CB-1702
     * This is caused by an additional request being made which is toggling the button. We need a way to relate the action button component is the relevant request
     */
    if (!this.action) {
      //Legacy support until action button moves over
      this.actionCallBack = () =>
        setTimeout(() => {
          this.enable()
          this.hide()
        }, 500)
      if (!this.manualHide) {
        actionEmitter.on("action-done", this.actionCallBack)
      }
    }
  },
  beforeUnmount() {
    if (!this.action) {
      actionEmitter.off("action-done", this.actionCallBack)
    }
  },
  methods: {
    disable() {
      this.waiting = true
    },
    show() {
      this.showOverlay = true
      this.showSpinnerTimeoutId = setTimeout(() => {
        this.showSpinner = true
        if (!this.action) {
          //Legacy, will be removed when action button moves over
          this.hideSpinnerTimeoutId = setTimeout(() => {
            this.showOverlay = false
            this.showSpinner = false
            this.waiting = false
          }, 5000)
        }
      }, this.spinnerDelayTime)
    },
    hide() {
      clearTimeout(this.hideSpinnerTimeoutId)
      clearTimeout(this.showSpinnerTimeoutId)
      this.showOverlay = false
      this.showSpinner = false
    },
    enable() {
      this.waiting = false
    },
    click() {
      this.disable()
      this.show()

      if (this.action) {
        const promise = this.action().finally(() => {
          this.enable()
          this.hide()
        })

        this.$emit("click", promise)

        if (this.onClick) {
          this.onClick(promise)
        }
      } else {
        this.$emit("click")
      }
    }
  }
}
</script>

<style scoped>
.overlay {
  pointer-events: visible;
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 99999999;
  transition: opacity 0.2s ease;
}

.spinner-background {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
  background: #ffffff94;
}

.spinner-border {
  position: relative;
  width: 7rem;
  height: 7rem;
  border-width: 0.5rem;
  z-index: 100000;
}
</style>
