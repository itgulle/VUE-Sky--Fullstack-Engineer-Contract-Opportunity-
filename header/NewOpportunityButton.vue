<template>
  <button
    class="btn header-item noti-icon waves-effect"
    title="Add Opportunity"
    @click="addOpportunity"
  >
    <i class="bx bx-folder-plus"></i>
  </button>
</template>

<script>
export default {
  name: "NewOpportunityButton",
  methods: {
    addOpportunity() {
      this.$store.commit("clients/setNewOpportunityActive", true)
      this.$store.commit("clientFile/refreshRightBar")
      setTimeout(() => {
        document.body.classList.toggle("right-bar-enabled")
      }, 500)
    }
  }
}
</script>
