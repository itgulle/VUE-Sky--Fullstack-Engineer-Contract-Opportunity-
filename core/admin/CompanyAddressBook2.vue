<template>
  <div>
    <div class="row">
      <div class="col-12">
        <div>
          <div>
            <div>
              <div class="row pb-3">
                <address-book-type-filter
                  :type="type"
                  @updateTypeFilter="updateTypeFilter"
                >
                </address-book-type-filter>
                <slot />
              </div>
              <div class="row pb-3">
                <div class="col">
                  <div class="table-responsive table-responsive mb-0">
                    <b-table
                      id="addressBookTable"
                      hover
                      no-sort-reset
                      class="datatables"
                      :items="addresses"
                      :fields="headers"
                      :busy="loading"
                      responsive="sm"
                      v-model:sort-by="sortBy"
                      v-model:sort-desc="addressSortDesc"
                      :per-page="perPage"
                      :current-page="currentPage"
                      :filter="search"
                      :filter-included-fields="filterOn"
                    >
                      <template #table-busy>
                        <div class="text-center text-primary my-2">
                          <b-spinner class="align-middle"></b-spinner>
                          <strong class="ms-2">Loading...</strong>
                        </div>
                      </template>
                      <template #cell(status)="data">
                        <span :class="[setStatusStyle(data.value)]">
                          {{ data.value }}
                        </span>
                      </template>
                      <template #cell(viewDetails)="row">
                        <button
                          :id="`addressBooksOpen${row.index}`"
                          class="btn btn-sm btn-primary btn-rounded"
                          @click="openDetails(row.item.id)"
                        >
                          Open/Edit
                        </button>
                      </template>
                    </b-table>
                    <p class="mt-3">
                      {{ message }}
                    </p>
                    <b-pagination
                      v-show="!loading"
                      v-model="currentPage"
                      :total-rows="totalRows"
                      :per-page="perPage"
                      align="right"
                      @page-click="paginationButtonClick"
                    ></b-pagination>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex"
import addressBookTypeFilter from "@/components/ux/shared/addressBook/AddressBookTypeFilter.vue"
import { datatableConfig } from "@/data/datatable-config"

export default {
  name: "CompanyAddressBook",
  components: { addressBookTypeFilter },
  props: {
    search: {
      type: String,
      required: false,
      default: null
    },
    loading: {
      type: Boolean,
      default: false
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
      sortBy: "business_name",
      addressSortDesc: false,
      columnDetails: [],
      activeFilter: "All",
      pagination: true,
      url: "",
      type: "company",
      perPage: 25,
      currentPage: 1,
      filter: null,
      filterOn: []
    }
  },
  computed: {
    ...mapGetters({
      addressBookCompanies: "addressBook/companies",
      branches: "addressBook/addressBookBranches",
      referralManagementConfig: "referrals/referralManagementConfig"
    }),
    message() {
      if (this.totalRows < 1) {
        return "Showing 0 entries"
      } else {
        var from = (this.currentPage - 1) * Number(this.perPage) + 1
        var to = this.currentEndRow(from)
        return `Showing ${from} to ${to} out of ${this.totalRows} entries`
      }
    },
    totalRows() {
      return this.addresses.length
    },
    isMemberFirmReferralPartnerManagementEnabled() {
      return this.referralManagementConfig.data?.config
        ?.is_member_firm_referral_partner_management_enabled
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
      itemList = itemList.map(
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
          referral_partner,
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
            business_referral_partner_status_key:
              referral_partner?.business_referral_partner_status_key === "active"
                ? "Active"
                : "Inactive",
            id
          }
        }
      )

      return itemList
    },
    headers() {
      const fields = []

      // retrieve the details for all columns
      const columnNames = [...this.columnDetails]

      if (columnNames && columnNames.length > 0) {
        //adds a checkbox as the first colunm, when required
        if (this.checkbox) fields.push({ key: "Select", label: " ", sortable: false })

        // set each column with details from config
        columnNames.forEach((columnItem) => {
          let newColumn = {
            label: columnItem.label,
            key: columnItem.key,
            sortable: columnItem.sortable
          }

          // add all other properties
          newColumn.classContent = columnItem.classContent
          newColumn.thStyle = columnItem.thStyle
          newColumn.fontColour = columnItem.fontColour
          newColumn.currency = columnItem.currency
          newColumn.link = columnItem.link
          newColumn.linkWhitelist = [...columnItem.linkWhitelist]
          newColumn.linkBlacklist = [...columnItem.linkBlacklist]

          fields.push(newColumn)
        })

        if (this.isMemberFirmReferralPartnerManagementEnabled) {
          fields.push({
            key: "business_referral_partner_status_key",
            label: "Referral Partner",
            thStyle: { width: "110px" },
            sortable: true
          })
        }

        fields.push({
          key: "viewDetails",
          label: "View Details",
          thStyle: { width: "110px" },
          sortable: false
        })
      }
      return fields
    }
  },
  async created() {
    this.tableConfig = datatableConfig

    const currentScreen = this.tableConfig.find(
      (o) => o.page === "addressbook" && o.component === "company"
    )

    if (currentScreen) this.columnDetails = [...currentScreen.headers]
  },
  beforeUnmount() {
    clearTimeout(this.$_debounceTimer)
  },
  methods: {
    updateTypeFilter(filter) {
      this.activeFilter = filter
      this.currentPage = 1
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
            params: { businessId: id.id }
          })
          break
        default:
          break
      }
    },
    setStatusStyle(value) {
      switch (value.toLowerCase()) {
        case "active":
          return "text-success fw-bold text-capitalize"
        default:
          return "text-danger fw-bold text-capitalize"
      }
    },
    debounceCriteria(value) {
      clearTimeout(this.$_debounceTimer)
      this.$_debounceTimer = setTimeout(() => {
        this.filter = value
      }, 200)
    },
    setFilter(updatedFilter) {
      this.activeFilter = updatedFilter
    },
    setActiveFilterLink(activeLink) {
      if (this.activeFilter === activeLink) {
        return "text-primary text-decoration-underline active"
      } else {
        return "text-secondary"
      }
    },
    currentEndRow(startIndex) {
      let totalPages = Math.ceil(this.totalRows / this.perPage)
      if (totalPages === this.currentPage) {
        return this.totalRows
      } else {
        return startIndex + Number(this.perPage) - 1
      }
    },
    paginationButtonClick(_, pageNumber) {
      this.currentPage = pageNumber
    },
    openDetails(id) {
      this.$router.push({
        name: "company-details-view",
        params: { businessId: id }
      })
    }
  }
}
</script>
