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
      :items="branches"
      :buttons="branchButtons"
      sortby-name="branch"
      :loading="loading"
      @button-selected="buttonClicked"
    />
  </div>
</template>
<script>
import addressBookTypeFilter from "@/components/ux/shared/addressBook/AddressBookTypeFilter.vue"
export default {
  name: "BranchesAddressBook",
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
      default: "branches"
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
      type: "branch"
    }
  },
  computed: {
    branchButtons() {
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
    branches() {
      let itemList = []
      if (this.activeFilter === "All") {
        return [...this.tableData]
      } else {
        itemList = [
          ...this.tableData.filter((a) => {
            return a.company_type.includes(this.activeFilter)
          })
        ]
      }
      return itemList
    }
  },
  methods: {
    updateTypeFilter(filter) {
      this.activeFilter = filter
    },
    buttonClicked(buttonName, branch) {
      const hyphen = buttonName.lastIndexOf("-")
      const name = buttonName.substring(hyphen + 1)
      switch (name) {
        case "branch":
        case "Open/Edit":
        case "business_branch_name":
          this.$router.push({
            name: "branch-details-view",
            params: { businessId: branch.business_id, branchId: branch.id }
          })
          break
        case "branch-introducer_link":
          console.log("TODO: go to introducer page, id:" + branch.id)
          break
        default:
          break
      }
    }
  }
}
</script>
