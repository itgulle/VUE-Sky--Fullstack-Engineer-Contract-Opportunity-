<template>
  <div>
    <div v-show="searchData && searchData.length > 0" id="dropdown_section">
      <div class="section-header">{{ sectionHeadingForType(type) }}</div>
      <div class="section-list">
        <ul>
          <li
            v-for="(item, index) in searchData"
            :key="index"
            @click="navToClientOrCase(item.type, item.clientId, item.caseId)"
          >
            {{ item.description }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "SearchbarDropdownSections",
  props: {
    data: {
      type: Array,
      required: true
    },
    type: {
      type: String,
      required: true
    }
  },
  computed: {
    searchData() {
      return [...this.data]
    }
  },
  methods: {
    sectionHeadingForType(type) {
      if (type == "client") return "Clients"
      if (type == "case") return "Cases"
      return type
    },
    async navToClientOrCase(type, clientId, caseId) {
      if (type == "case") {
        this.$store.commit("cases/clearSelectedSnapshotId")
        this.$store.commit("clientFile/setLoadedClientId", "")
        await this.$store.dispatch("clientFile/loadClient", clientId)
        await this.$store.dispatch("cases/getCase", caseId)
        this.$store.commit("clientFile/setShowCaseClients", true)
        await this.$store.dispatch("clientFile/loadCompanies")
        await this.$store.dispatch("cases/loadAdmins")
        await this.$store.dispatch("advisors/loadAdvisors")
        await this.$store.dispatch("cases/loadComplianceUsers")
        await this.$store.dispatch("cases/loadAllTeamMembers")

        this.$router.push({
          name: "case-overview",
          params: {
            caseId: caseId,
            id: clientId
          }
        })
      } else {
        // If already in clientFile then reload client otherwise the route guard will handle the reload.
        if (this.$route.matched.some((route) => route.path.includes("clientfile"))) {
          this.$store.dispatch("clientFile/loadClient", clientId).then(() => {
            this.$router.push({
              name: "client-cases",
              params: { id: clientId }
            })
          })
        } else {
          this.$router.push({
            name: "client-cases",
            params: { id: clientId }
          })
        }
      }
    }
  }
}
</script>

<style scoped>
.section-header {
  font-size: 14px;
  font-weight: 600;
}

.section-list {
  position: relative;
  top: calc(100% - 1px);
  left: 0;
  padding: 5px 0px 10px;
  margin: 0;
  width: 100%;
  max-height: 150px;
  min-width: 60px;
  overflow-y: auto;
  text-align: left;
}

.section-list ul {
  padding: 0px;
}
.section-list ul > li {
  line-height: 1.42857143;
  /* Normalize line height */
  display: block;
  padding: 3px 20px;
  clear: both;
  color: #333;
  /* Overrides most CSS frameworks */
  white-space: nowrap;
  cursor: pointer;
}

.section-list ul > li:hover {
  background-color: #5897fb;
  color: #fff;
}
</style>
