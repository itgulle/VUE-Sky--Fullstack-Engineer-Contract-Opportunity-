<template>
  <div>
    <div class="btn-group company-name-btns">
      <div v-for="(company, index) in companies" :key="company.id">
        <input
          :id="`btnCompany${index}`"
          type="radio"
          class="btn-check"
          name="btnCompanies"
          autocomplete="off"
          :checked="index === selectedIndex"
          @click="updateSelectedCompany(company, index)"
        />
        <label
          class="btn btn-outline-primary px-3"
          :class="[index !== 0 ? 'label-display-left' : '']"
          :for="`btnCompany${index}`"
        >
          {{ company.business_name }}</label
        >
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    selectedIndex: {
      type: Number,
      default: () => 0
    },
    companies: {
      type: Array,
      required: true
    }
  },
  methods: {
    updateSelectedCompany(company, index) {
      this.$emit("updateCompanyFilter", {
        id: company.id,
        name: company.business_name,
        index: index
      })
    }
  }
}
</script>

<style scoped>
.company-name-btns div:first-child label {
  border-start-start-radius: 6px;
  border-end-start-radius: 6px;
}
.company-name-btns div label {
  border-radius: 0px;
}
.label-display-left {
  border-left: 0px;
}
.company-name-btns div:last-child label {
  border-end-end-radius: 6px;
  border-start-end-radius: 6px;
}
</style>
