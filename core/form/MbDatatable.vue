<template>
  <div>
    <div v-if="!pagination" class="table-responsive mb-0">
      <b-table
        :id="`${page}-${component}-non-paging`"
        show-empty
        hover
        fixed
        no-sort-reset
        style="overflow: scroll"
        class="datatables me-3"
        :items="loadItems"
        :fields="headers"
        :busy="loading"
        responsive="sm"
        v-model:sort-by="sortBy"
        v-model:sort-desc="sortDesc"
        :filter="filtertable"
        :filter-included-fields="filterOn"
      >
        <!-- checkbox section -->
        <template v-if="checkbox" #cell(select)="data">
          <div class="form-check font-size-16">
            <input
              :id="`${component}Check${data.index}`"
              class="form-check-input"
              type="checkbox"
            />
            <label
              class="form-check-label"
              :for="`${component}Check${data.index}`"
            ></label>
          </div>
        </template>
        <!-- fields section -->
        <template #cell()="data">
          <!-- link field -->
          <div
            v-if="
              data.field.link &&
              linkCellChecker(
                data.field.linkWhitelist,
                data.field.linkBlacklist,
                data.value
              )
            "
            :class="data.field.classContent"
          >
            <a
              :id="`${component}link${data.index}`"
              href="javascript:void(0);"
              class="text-decoration-underline"
              :class="data.field.classContent"
              @click="buttonClicked(`${component}-${data.field.key}`, data.item)"
            >
              {{ data.value }}
            </a>
          </div>
          <!-- field with the font colour set in config -->
          <p
            v-else-if="data.field.fontColour"
            class="m-0 pt-1"
            :class="combineClassContentAndFontColour(data.field.classContent, data.value)"
          >
            {{ formatCellValue(data.value) }}
          </p>
          <!-- icon field -->
          <span v-else-if="data.field.icon">
            icon field
            <slot name="icon" :data="data" />
          </span>
          <!-- N/A value -->
          <p v-else-if="data.value === 'N/A'" class="m-0 pt-1">N/A</p>
          <!-- default -->
          <div v-else>
            <p class="m-0 pt-1" :class="data.field.classContent">
              {{ formatCellValue(data.value, false, data.field.currency) }}
              <br />
              <small class="fw-light" style="font-size: 12px">
                {{ formatCellValue(data.value, true, false) }}
              </small>
            </p>
          </div>
        </template>
        <template #table-busy>
          <div class="text-center text-primary my-2">
            <b-spinner class="align-middle"></b-spinner>
            <strong class="ms-2">Loading...</strong>
          </div>
        </template>
        <!-- button/link section -->
        <template #cell(viewDetails)="data">
          <span v-for="(buttonItem, index) in data.field.buttons" :key="index">
            <button
              v-if="buttonItem.type === 'button'"
              :id="`btn${component}-vd-${data.index}`"
              type="button"
              class="btn btn-primary btn-sm btn-rounded ms-0 me-3 mt-1"
              :class="buttonItem.classObject"
              :disabled="buttonItem.disabled"
              @click="buttonClicked(`${component}-${buttonItem.caption}`, data.item)"
            >
              {{ buttonItem.caption }}
            </button>
            <button
              v-else
              :id="`link${component}-vd-${data.index}`"
              type="button"
              class="btn btn-link ms-3 mb-1"
              :class="buttonItem.classObject"
              :disabled="buttonItem.disabled"
              @click="buttonClicked(`${component}-${buttonItem.caption}`, data.item)"
            >
              {{ buttonItem.caption }}
            </button>
          </span>
        </template>
      </b-table>
    </div>
    <div v-if="pagination" class="table-responsive mb-0">
      <b-table
        :id="`${page}-${component}-paging`"
        show-empty
        hover
        fixed
        no-sort-reset
        class="datatables me-3"
        :items="loadItems"
        :fields="headers"
        responsive="sm"
        :busy="loading"
        v-model:sort-by="sortBy"
        v-model:sort-desc="sortDesc"
        :filter="filtertable"
        :filter-included-fields="filterOn"
        :per-page="perPage"
        :current-page="currentPage"
        @filtered="onFiltered"
      >
        <!--Section just for address book-->

        <template #cell(defaultPhone)="data">
          <span> {{ data.item.defaultPhone }} </span> <br />
          <a
            v-if="data.item.website"
            target="_blank"
            :href="websiteLink(data.item.website)"
          >
            Visit Website
          </a>
        </template>
        <!-- End of address book section -->

        <!-- checkbox section -->
        <template v-if="checkbox" #cell(select)="data">
          <div class="form-check font-size-16">
            <input
              :id="`${component}Check${data.index}`"
              class="form-check-input"
              type="checkbox"
            />
            <label
              class="form-check-label"
              :for="`${component}Check${data.index}`"
            ></label>
          </div>
        </template>
        <template #table-busy>
          <div class="text-center text-primary my-2">
            <b-spinner class="align-middle"></b-spinner>
            <strong class="ms-2">Loading...</strong>
          </div>
        </template>
        <!-- fields section -->
        <template #cell()="data">
          <!-- link field -->
          <div
            v-if="
              data.field.link &&
              linkCellChecker(
                data.field.linkWhitelist,
                data.field.linkBlacklist,
                data.value
              )
            "
            :class="data.field.classContent"
          >
            <a
              :id="`${component}link${data.index}`"
              href="javascript:void(0);"
              class="text-decoration-underline"
              :class="data.field.classContent"
              @click="buttonClicked(`${component}-${data.field.key}`, data.item)"
            >
              {{ data.value }}
            </a>
          </div>
          <!-- field with the font colour set in the config-->
          <p
            v-else-if="data.field.fontColour"
            class="m-0 pt-1"
            :class="combineClassContentAndFontColour(data.field.classContent, data.value)"
          >
            {{ formatCellValue(data.value) }}
          </p>
          <!-- icon field -->
          <span v-else-if="data.field.icon">
            icon field
            <slot name="icon" :data="data" />
          </span>
          <!-- default  -->
          <div v-else>
            <p class="m-0 pt-1" :class="data.field.classContent">
              {{ formatCellValue(data.value, false, data.field.currency) }}
              <br />
              <small class="fw-light" style="font-size: 12px">
                {{ formatCellValue(data.value, true, false) }}
              </small>
            </p>
          </div>
        </template>
        <!-- button/link section -->
        <template #cell(viewDetails)="data">
          <span v-for="(buttonItem, index) in data.field.buttons" :key="index">
            <button
              v-if="buttonItem.type === 'button'"
              :id="`btn${component}-vd-${data.index}`"
              type="button"
              class="btn btn-primary btn-sm btn-rounded ms-0 me-3 mt-1"
              :class="buttonItem.classObject"
              :disabled="buttonItem.disabled"
              @click="buttonClicked(`${component}-${buttonItem.caption}`, data.item)"
            >
              {{ buttonItem.caption }}
            </button>
            <button
              v-else
              :id="`link${component}-vd-${data.index}`"
              type="button"
              class="btn btn-link ms-3 mb-1"
              :class="buttonItem.classObject"
              :disabled="buttonItem.disabled"
              @click="buttonClicked(`${component}-${buttonItem.caption}`, data.item)"
            >
              {{ buttonItem.caption }}
            </button>
          </span>
        </template>
      </b-table>
      <!-- pagination section -->
      <div class="d-flex justify-content-between align-content-center m-4 ms-0">
        <p class="mt-1">
          {{ paginationMessage }}
        </p>
        <b-pagination
          v-model="currentPage"
          :total-rows="totalRows"
          :per-page="perPage"
          :aria-controls="`${page}-${component}-paging`"
          align="right"
        ></b-pagination>
      </div>
    </div>
  </div>
</template>
<script>
import { datatableConfig } from "@/data/datatable-config"
import { functionService } from "@/global/modules/helpers"

export default {
  props: {
    /**
     * Page and Component props are used to find the screen settings in the Data Dictionary / config file
     *
     */
    page: {
      type: String,
      required: true
    },
    component: {
      type: String,
      required: true
    },
    items: {
      type: Array,
      default: function () {
        return []
      }
    },
    /**
     * Set the pagination attribute to display pagination at the button of the table
     *
     * <mb-datatable pagination />
     */
    pagination: {
      type: Boolean,
      required: false,
      default: true
    },
    /**
     * Set the number of rows to display (default = 10)
     *
     * <mb-datatable per-page=15 />
     */
    perPage: {
      type: Number,
      required: false,
      default: 25
    },
    /**
     * Set the checkbox attribute to display the checkbox in the first column
     *
     * <mb-datatable checkbox />
     */
    checkbox: {
      type: Boolean,
      required: false,
      default: false
    },
    /**
     * Set the sortbyName props to init sorting
     *
     * <mb-datatable sortby-name="client" />
     */
    sortbyName: {
      type: String,
      required: false,
      default: ""
    },
    sortbyDesc: {
      type: Boolean,
      required: false,
      default: false
    },
    /**
     * Set buttons props when button(s)/link(s) are required for the last column
     * e.g. mortgageButtons() {return {label: "view Details", buttonNames: [{ type: "button", caption: "open",classObject: {"text-danger": true, "text-decoration-underline": true}}]}}
     *
     * <mb-datatable :buttons="mottageButtons" />
     */
    buttons: {
      type: Object,
      required: false,
      default: null
    },
    /**
     * Set the filtertable props when a search control is on the page
     *
     * <mb-datatable :filtertable="filter" />
     */
    filtertable: {
      type: String,
      required: false,
      default: null
    },
    loading: {
      type: Boolean,
      required: false,
      default: false
    }
  },
  emits: ["button-selected"],
  data() {
    return {
      columnDetails: [],
      filterOn: [],
      sortBy: "",
      sortDesc: false,
      totalRows: 1,
      currentPage: 1
    }
  },
  computed: {
    itemsSortBy() {
      return this.sortBy
    },
    itemsSortDesc() {
      return this.sortDesc
    },
    loadItems() {
      // add the cell variant to each row
      this.onFiltered(this.items)
      const itemList = this.items.map((item) => {
        return {
          ...item,
          _cellVariants: this.getCellVariant(item)
        }
      })
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

        // if button(s)/links are required for the last column
        if (this.buttons) {
          fields.push({
            key: "viewDetails",
            label: this.buttons.label,
            thStyle: { width: "110px" },
            buttons: this.buttons.buttonNames,
            sortable: false
          })
        }
      }
      return fields
    },
    paginationMessage() {
      if (this.totalRows < 1) {
        return "Showing 0 entries"
      } else {
        var from = (this.currentPage - 1) * Number(this.perPage) + 1
        var to = this.currentEndRow(from)
        return " Showing " + from + " to " + to + " out of " + this.totalRows + " entries"
      }
    }
  },
  created() {
    this.sortBy = this.sortbyName
    this.sortDesc = this.sortbyDesc
    this.tableConfig = datatableConfig

    //fetch the details from the config data
    //TODO: move to store
    const currentScreen = this.tableConfig.find(
      (o) => o.page === this.page && o.component === this.component
    )

    if (currentScreen) this.columnDetails = [...currentScreen.headers]
  },
  methods: {
    formatCellValue(value, secondSring, currency) {
      if (value === "N/A") {
        return value
      }

      const results = [...value.toString().split("|")]
      if (secondSring) {
        if (results && results.length > 1) {
          return results[1]
        } else {
          return ""
        }
      } else {
        if (currency) {
          return functionService.formatNumberAsCurrency(results[0])
        } else {
          return results[0]
        }
      }
    },
    linkCellChecker(whiteList, blackList, value) {
      // Set to true for the cell value when a link is required but no whitelist or blacklist is provided
      let returnValue = true
      if (blackList && blackList.length > 0 && blackList.includes(value)) {
        returnValue = false
      }
      if (whiteList && whiteList.length > 0) {
        returnValue = whiteList.includes(value)
      }
      return returnValue
    },
    buttonClicked(buttonName, item) {
      //send button name and row id for API calls
      this.$emit("button-selected", buttonName, item)
    },
    getCellVariant(rowData) {
      //generate the _cellvariant object
      const cellVariants = {}
      let variant = ""

      // will probably fetch this from the backend if the list requires updating
      const bgDangerValues = ["Incomplete", "Today", "Outstanding"]

      const bgSuccessValues = ["Complete", "Done"]

      // retrieve all the columns
      const columns = [...this.columnDetails]

      columns.forEach((columnItem) => {
        if (columnItem.bgColour && columnItem.key in rowData) {
          // add sucess bg
          if (bgSuccessValues.includes(rowData[columnItem.key])) {
            variant = "success"
            cellVariants[columnItem.key] = variant
          }
          // bg colour for date
          if (
            rowData[columnItem.key] &&
            rowData[columnItem.key].length === 10 &&
            !isNaN(new Date(rowData[columnItem.key]))
          ) {
            variant = "success"
            cellVariants[columnItem.key] = variant
          }
          // add danger bg
          if (bgDangerValues.includes(rowData[columnItem.key])) {
            variant = "danger"
            cellVariants[columnItem.key] = variant
          }
        }
      })

      return cellVariants
    },
    combineClassContentAndFontColour(cssContent, value) {
      const bgDangerValues = ["Inactive"]
      const bgSuccessValues = ["Active"]

      if (bgSuccessValues.includes(value)) {
        return `${cssContent} text-success`
      }

      if (bgDangerValues.includes(value)) {
        return `${cssContent} text-danger`
      }

      return cssContent
    },
    currentEndRow(startIndex) {
      let totalPages = Math.ceil(this.totalRows / this.perPage)
      if (totalPages === this.currentPage) {
        return this.totalRows
      } else {
        return startIndex + Number(this.perPage) - 1
      }
    },
    onFiltered(filteredItems) {
      this.totalRows = filteredItems.length
      this.currentPage = 1
    },
    websiteLink(link) {
      if (!link.startsWith("http")) {
        return "http://" + link
      }
      return link
    }
  }
}
</script>
