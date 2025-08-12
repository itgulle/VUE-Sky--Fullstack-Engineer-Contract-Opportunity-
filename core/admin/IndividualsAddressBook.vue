<template>
  <div>
    <div v-if="includeTopParagraph" class="mt-3 mb-4">
      You need to create a company contact before being able to add a branch or individual
    </div>
    <address-book-type-filter
      v-if="includeTypeFilter"
      :type="type"
      @updateTypeFilter="updateTypeFilter"
    >
    </address-book-type-filter>
    <slot />
    <mb-datatable
      page="addressbook"
      :component="tableComponent"
      :filtertable="search"
      :items="individuals"
      :buttons="individualButtons"
      sortby-name="business_name"
      :loading="loading"
      @button-selected="buttonClicked"
    />
  </div>
</template>
<script>
import addressBookTypeFilter from "@/components/ux/shared/addressBook/AddressBookTypeFilter.vue"
export default {
  name: "IndividualAddressBook",
  components: { addressBookTypeFilter },
  props: {
    search: {
      type: String,
      required: false,
      default: null
    },
    rating: {
      type: Number,
      required: false,
      default: () => 0.0
    },
    includeTypeFilter: {
      type: Boolean,
      required: false,
      default: true
    },
    tableComponent: {
      type: String,
      required: false,
      default: "individuals"
    },
    includeTopParagraph: {
      type: Boolean,
      required: false,
      default: true
    },
    tableData: {
      type: Array,
      default: () => [],
      required: true
    },
    loading: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      activeFilter: "All",
      type: "individual"
    }
  },
  computed: {
    individualButtons() {
      return {
        label: "View Details",
        filter: null,
        filterOn: [],
        buttonNames: [
          {
            type: "button",
            caption: "Open/Edit",
            classObject: {
              "bg-primary": true
            }
          }
        ]
      }
    },
    individuals() {
      let itemList = []
      if (this.activeFilter === "All") {
        return [...this.tableData]
      } else {
        itemList = [
          ...this.tableData.filter((a) => {
            return a.company_type === this.activeFilter
          })
        ]
      }
      return itemList
    }
  },
  methods: {
    updateTypeFilter(filter) {
      this.activeFilter = filter
      this.currentPage = 1
    },
    buttonClicked(buttonName, individual) {
      const hyphen = buttonName.lastIndexOf("-")
      const name = buttonName.substring(hyphen + 1)

      switch (name) {
        case "business_name":
          this.$router.push({
            name: "company-details-view",
            params: { businessId: individual.business_id }
          })
          break
        case "branch":
          this.$router.push({
            name: "branch-details-view",
            params: { businessId: individual.business_id, branchId: individual.branch_id }
          })
          break
        case "Open/Edit":
        case "fullname":
          this.$router.push({
            name: "edit-individual",
            params: {
              businessId: individual.business_id,
              individualId: individual.id
            }
          })
          break
        default:
          break
      }
    }
  }
}
</script>
