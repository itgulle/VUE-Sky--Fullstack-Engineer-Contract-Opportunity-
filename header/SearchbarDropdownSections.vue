<template>
  <div>
    <div v-show="searchData && searchData.length > 0" id="dropdown_content">
      <searchbar-dropdown-section
        :data="searchDataForType('case')"
        :type="'case'"
      ></searchbar-dropdown-section>
      <searchbar-dropdown-section
        :data="searchDataForType('client')"
        :type="'client'"
      ></searchbar-dropdown-section>
    </div>
  </div>
</template>

<script>
import SearchbarDropdownSection from "./SearchbarDropdownSection.vue"

export default {
  name: "SearchbarDropdownSections",
  components: {
    SearchbarDropdownSection
  },
  props: {
    data: {
      type: Array,
      required: true
    }
  },
  computed: {
    searchData() {
      return [...this.data]
    }
  },

  methods: {
    searchDataForType(type) {
      return [...this.data].filter((x) => x.type == type)
    }
  }
}
</script>
<style scoped></style>
