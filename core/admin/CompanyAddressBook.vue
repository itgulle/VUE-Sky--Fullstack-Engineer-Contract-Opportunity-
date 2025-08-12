<template>
  <div>
    <address-book-type-filter :type="type" @updateTypeFilter="updateTypeFilter">
    </address-book-type-filter>
    <slot />
    <mb-datatable
      page="addressbook"
      component="company"
      :filtertable="search"
      :items="addresses"
      :buttons="addressButtons"
      sortby-name="business_name"
      @button-selected="buttonClicked"
    >
    </mb-datatable>
  </div>
</template>
<script>
import { mapGetters } from "vuex"
import addressBookTypeFilter from "@/components/ux/shared/addressBook/AddressBookTypeFilter.vue"
export default {
  name: "CompanyAddressBook",
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
    }
  },
  emits: ["add-address"],
  data() {
    return {
      activeFilter: "All",
      url: "",
      type: "company"
    }
  },
  computed: {
    ...mapGetters({
      addressBookCompanies: "addressBook/companies"
    }),
    addressButtons() {
      return {
        label: "View Details",
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
    addresses() {
      let itemList = []
      if (this.activeFilter === "All") {
        itemList = [...this.addressBookCompanies]
      } else {
        itemList = [
          ...this.addressBookCompanies.filter((a) => {
            return a.business_service_type_key?.includes(this.activeFilter)
          })
        ]
      }
      return itemList.map(
        ({
          business_name,
          type_text,
          addressStr,
          defaultPhone,
          emails,
          phones,
          website,
          branch_count,
          individuals_count,
          introducer,
          id
        }) => {
          return {
            business_name: `${business_name} | ${type_text}`,
            addressStr,
            defaultPhone,
            emails,
            phones,
            website,
            branch_count,
            individuals_count,
            business_introducer_status_key:
              introducer?.business_introducer_status_key === "active"
                ? "Active"
                : "Inactive",
            id
          }
        }
      )
    }
  },
  methods: {
    updateTypeFilter(filter) {
      this.activeFilter = filter
    },
    buttonClicked(buttonName, id) {
      const reg = /^(https:\/\/)/
      switch (buttonName) {
        case "company-website":
          this.url = !reg.test(id.website) ? "http://" + id.website : id.website
          window.open(this.url, "_blank")
          break
        case "company-business_name":
        case "company-Open/Edit":
          this.$router.push({
            name: "company-details-view",
            params: { companyId: id.id }
          })
          break
        default:
          break
      }
    }
  }
}
</script>
