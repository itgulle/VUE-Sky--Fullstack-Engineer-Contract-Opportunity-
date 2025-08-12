<template>
  <div>
    <form class="app-search search-extend d-none d-lg-block">
      <div class="dropdown">
        <input
          v-model="searchText"
          type="text"
          class="form-control"
          placeholder="Search by client name or case reference"
          @input="(event) => debounceCriteria(event.target.value)"
        />
        <span class="mdi mdi-magnify"></span>
        <div
          v-show="searchData.length > 0"
          id="dropdown_content"
          class="dropdown-content"
        >
          <searchbar-dropdown-sections :data="searchData"></searchbar-dropdown-sections>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
import { useMbUser } from "@mortgage-brain/mb-user-vue3-plugin"
import SearchbarDropdownSections from "./SearchbarDropdownSections.vue"
import { cdsUrl } from "@/global/modules/helpers/urlHelpers"
export default {
  components: {
    SearchbarDropdownSections
  },
  data() {
    return {
      searchText: "",
      searchData: []
    }
  },
  watch: {
    // Clear dropdown on route change
    $route() {
      this.searchData = []
      this.searchText = ""
    }
  },
  beforeUnmount() {
    clearTimeout(this.$_debounceTimer)
  },
  methods: {
    async search(text) {
      if (text.length > 1) {
        const { axiosInstance } = useMbUser()
        const payload = {
          include_cases: true,
          include_clients: true,
          search_text: text
        }
        let result = await axiosInstance.post(cdsUrl(`query/autocompletesearch`), payload)
        this.searchData = result.data
      } else {
        this.searchData = []
      }
    },
    debounceCriteria(text) {
      clearTimeout(this.$_debounceTimer)
      this.$_debounceTimer = setTimeout(() => {
        this.search(text)
      }, 300)
    }
  }
}
</script>

<style scoped>
.search-extend {
  width: 500px;
}
.dropdown {
  position: relative;
}
.dropdown-content {
  box-sizing: border-box;
  position: absolute;
  top: calc(100% - 1px);
  left: 0;
  z-index: 1000;
  padding: 5px 0px 10px;
  margin: 0;
  width: 100%;
  /* height: 300px; */
  max-height: 350px;
  min-width: 150px;
  overflow-y: hidden;
  box-shadow: 0px 3px 6px 0px rgba(0, 0, 0, 0.15);
  border: 1px solid rgba(60, 60, 60, 0.26);
  border-top-style: none;
  border-radius: 0 0 4px 4px;
  text-align: left;
  list-style: none;
  background: #fff;
}
</style>
