<template>
  <div v-if="messageText" :class="baseClass">
    <div :class="messageClass">
      <button
        v-if="showDismiss"
        type="button"
        class="btn-close"
        data-bs-dismiss="alert"
      ></button>
      {{ messageText }} <slot></slot>
    </div>
  </div>
</template>

<script>
export default {
  name: "StyledMessage",
  props: {
    message: {
      type: String,
      required: false,
      default: null
    },
    template: {
      type: String,
      required: false,
      default: "validation"
    },
    customType: {
      type: String,
      required: false,
      default: null,
      validator(value) {
        // The value must match one of these strings
        return [null, "text", "alert-dismissible", "alert"].includes(value) //use array as one of the options may be removed in the future
      }
    },
    customLevel: {
      type: String,
      required: false,
      default: null,
      validator(value) {
        // The value must match one of these strings
        //these are supported by bootstrap for both alert-{level} and text-{level}
        return [
          null,
          "primary",
          "secondary",
          "success",
          "danger",
          "warning",
          "info",
          "light",
          "dark"
        ].includes(value)
      }
    },
    /**
     * Used to override default padding
     */
    baseClass: {
      type: String,
      required: false,
      default: "styled-message-container"
    }
  },
  computed: {
    type() {
      if (this.customType) return this.customType
      return this.selectedTemplate.type
    },
    level() {
      if (this.customLevel) return this.customLevel
      return this.selectedTemplate.level
    },
    selectedTemplate() {
      let defaultTemplate = { type: "text", level: "danger" }
      switch (this.template) {
        case "validation":
          return { type: "text", level: "danger" }
        case "error":
          return { type: "alert-dismissible", level: "danger" }
        case "warning":
          return { type: "alert-dismissible", level: "warning" }
        default:
          console.error(
            `Not Implemented StyledMessage > selectedTemplate > ${this.template}`
          )
          return defaultTemplate
      }
    },
    messageText() {
      return this.message
    },
    showDismiss() {
      return this.type === "alert-dismissible"
    },
    messageClass() {
      switch (this.type) {
        case "text":
          return `text-${this.level}`
        case "alert-dismissible":
          return `alert alert-${this.level} alert-dismissible`
        case "alert":
          return `alert alert-${this.level}`
        default:
          console.error(`Not Implemented StyledMessage > cssClass > ${this.type}`)
          return `text-${this.level}`
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.styled-message-container {
  margin-top: 1rem;
  margin-bottom: 1rem;
}
</style>
