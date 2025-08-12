<template>
  <div>
    <div class="row">
      <div class="col d-flex justify-content-end">
        <button
          type="button"
          :disabled="pageNavData.prevData.disabled"
          :class="[
            'btn waves-effect waves-light fw-bold',
            { 'btn-secondary disabled': pageNavData.prevData.disabled },
            { 'btn-primary': !pageNavData.prevData.disabled }
          ]"
          @click="navigateTo(pageNavData.prevData.url)"
        >
          <i class="bx bx-left-arrow-alt align-middle"></i>
          {{ pageNavData.prevData.label }}
        </button>
        <button
          :disabled="pageNavData.nextData.disabled"
          :class="[
            'btn waves-effect waves-light fw-bold ms-2',
            { 'btn-secondary disabled': pageNavData.nextData.disabled },
            { 'btn-primary': !pageNavData.nextData.disabled }
          ]"
          @click="navigateTo(pageNavData.nextData.url)"
        >
          {{ pageNavData.nextData.label }}
          <i class="bx bx-right-arrow-alt align-middle"></i>
        </button>
      </div>
    </div>
    <div v-if="pageNavData.exitData !== undefined" class="row exit-button-block">
      <div class="col d-flex justify-content-end">
        <button
          type="button"
          class="btn btn-outline-primary"
          @click="navigateTo(pageNavData.exitData.url)"
        >
          {{ pageNavData.exitData.label }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    pageNavData: {
      type: Object,
      default: null
    }
  },
  methods: {
    navigateTo(url) {
      this.$router.push({ name: url }).catch((error) => {
        console.info(error.message)
      })
    }
  }
}
</script>

<style lang="scss" scoped>
.exit-button-block {
  padding: 16px 0 0;
}
</style>
