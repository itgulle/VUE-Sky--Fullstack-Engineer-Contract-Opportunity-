<template>
  <header id="page-topbar">
    <div class="navbar-header">
      <div class="d-flex">
        <!-- LOGO -->
        <div class="navbar-brand-box p-0">
          <router-link to="/" class="logo logo-light">
            <span class="logo-sm">
              <img
                src="https://res.cloudinary.com/mortgage-brain-ltd/image/authenticated/s--SOBrJZpA--/v1662714409/Mortgage%20Brain%20Company%20Logos/MB-Favicon_Padded-WO_nmzdk5.png"
                alt=""
                height="50"
              />
            </span>
            <span class="logo-lg">
              <img
                src="https://res.cloudinary.com/mortgage-brain-ltd/image/authenticated/s---7kIh_GS--/v1626793719/Mortgage%20Brain%20Product%20Logos/CRM-Brain-Wide-Jul21-WHTMONO.png"
                alt=""
                height="50"
              />
            </span>
          </router-link>
        </div>

        <button
          id="vertical-menu-btn"
          type="button"
          class="btn btn-sm px-3 font-size-16 header-item waves-effect"
          @click="toggleSidebar"
        >
          <i
            class="mdi mdi-menu-open font-size-24"
            :class="{
              'mdi-menu-open--flipped': verticalMenuIconFlipped
            }"
          ></i>
        </button>

        <searchbar />
      </div>
      <div class="d-flex gap-2">
        <notification-drawer @linkClicked="handleNotificationRouting" />
        <new-opportunity-button />
        <calculator-dropdown />

        <!-- temp removal for mvp -->
        <!--<products-dropdown />-->

        <!-- temp removal for mvp -->
        <!--<support-menu />-->

        <mb-user-dropdown class="ms-3" />

        <div class="d-lg-inline-block align-self-center">
          <a :href="hubUrl">
            <img
              src="@/global/assets/images/hub-icon.png"
              alt="Unified Platform Hub"
              width="30"
              height="30"
              class="rounded-circle"
            />
          </a>
        </div>

        <!-- temp removal for mvp -->
        <!--<div class="dropdown d-inline-block">
          <button
            type="button"
            class="btn header-item noti-icon right-bar-toggle waves-effect"
            @click="openConfig"
          >
            <i class="bx bx-cog"></i>
          </button>
        </div>-->
      </div>
    </div>
  </header>
</template>

<script>
import { mapActions, mapGetters } from "vuex"
import CalculatorDropdown from "@/components/ux/header/CalculatorDropdown.vue"
import Searchbar from "@/components/ux/header/Searchbar.vue"
import NewOpportunityButton from "@/components/ux/header/NewOpportunityButton.vue"
import { generateRouteFromLinkInfo } from "@/global/modules/helpers/notificationLinkHelpers.js"
import { NotificationDrawer } from "@/plugins/components-plugin/entry.js"
//import ProductsDropdown from "@/components/ux/header/ProductsDropdown.vue"
//import SupportMenu from "@/components/ux/header/SupportMenu.vue"
import MbUserDropdown from "../../MbUserDropdown.vue"
import { gridBreakpoints } from "@/global/modules/enum/constants"
import { useMbProducts } from "@/global/modules/composables/useMbProducts"
export default {
  components: {
    CalculatorDropdown,
    Searchbar,
    NewOpportunityButton,
    NotificationDrawer,
    MbUserDropdown
    //ProductsDropdown,
    //SupportMenu
  },
  data() {
    return {
      hubUrl: null,
      windowWidth: null
    }
  },
  computed: {
    ...mapGetters({
      sidebarCollapsed: "layout/sidebarCollapsed",
      mobileSidebarCollapsed: "layout/mobileSidebarCollapsed",
      currentCase: "cases/currentCase"
    }),
    verticalMenuIconFlipped() {
      return this.windowWidth <= gridBreakpoints.lg
        ? this.mobileSidebarCollapsed
        : this.sidebarCollapsed
    }
  },
  mounted() {
    this.windowWidth = window.innerWidth
    window.addEventListener("resize", this.handleWindowResize)
    const { productUrls } = useMbProducts()
    this.hubUrl = `${productUrls.value.hubUrl}`
  },
  beforeUnmount() {
    window.removeEventListener("resize", this.handleWindowResize)
  },
  methods: {
    ...mapActions({
      toggleSidebar: "layout/toggleSidebar"
    }),
    openConfig() {
      this.$router.push({ name: "config-system-security" })
    },
    async handleNotificationRouting(linkInfo) {
      let route = generateRouteFromLinkInfo(linkInfo.linkInfo, linkInfo.linkIndex)

      if (linkInfo.linkInfo.link_ids.case_id !== this?.currentCase?.id) {
        // load everything for a Case that's not the current one
        this.$store.commit("cases/clearSelectedSnapshotId")
        this.$store.commit("clientFile/setLoadedClientId", "")
        await this.$store.dispatch("cases/getCase", linkInfo.linkInfo.link_ids.case_id)
        this.$store.commit("cases/setCurrentCaseId", linkInfo.linkInfo.link_ids.case_id)
        this.$store.commit("clientFile/setShowCaseClients", true)

        let clientId = this.$store.getters["cases/currentCase"].linked_clients[0]

        await this.$store.dispatch("clientFile/loadClient", clientId)
        await this.$store.dispatch("clientFile/loadCompanies")
        await this.$store.dispatch("cases/loadAdmins")
        await this.$store.dispatch("advisors/loadAdvisors")
        await this.$store.dispatch("cases/loadComplianceUsers")
        await this.$store.dispatch("cases/loadAllTeamMembers")
        await this.$store.dispatch("clientportal/loadUsers")
        await this.$store.dispatch("messages/loadMessages")

        this.$router.push(
          `/clients/${linkInfo.linkInfo.link_ids.client_id}/case/${linkInfo.linkInfo.link_ids.case_id}/messages/${linkInfo.linkInfo.link_ids.client_id}`
        )
      } else {
        this.$router.push(route)
      }
    },
    handleWindowResize() {
      this.windowWidth = window.innerWidth
    }
  }
}
</script>

<style lang="scss" scoped>
.mdi-menu-open {
  &--flipped {
    &:before {
      transform: rotate(180deg);
    }
  }
}
</style>
