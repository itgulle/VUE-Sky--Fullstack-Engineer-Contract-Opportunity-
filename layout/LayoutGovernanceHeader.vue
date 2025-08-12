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
              'mdi-menu-open--flipped': sidebarCollapsed
            }"
          ></i>
        </button>
      </div>
      <div class="d-flex gap-2">
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
      </div>
    </div>
  </header>
</template>

<script>
import { mapActions, mapGetters } from "vuex"
import MbUserDropdown from "../../MbUserDropdown.vue"
import { useMbProducts } from "@/global/modules/composables/useMbProducts"

export default {
  components: {
    MbUserDropdown
  },
  data() {
    return {
      hubUrl: null
    }
  },
  mounted() {
    const { productUrls } = useMbProducts()
    this.hubUrl = `${productUrls.hubUrl}`
  },
  computed: {
    ...mapGetters({
      sidebarCollapsed: "layout/sidebarCollapsed"
    })
  },
  methods: {
    ...mapActions({
      toggleSidebar: "layout/toggleSidebar"
    })
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

.navbar-brand-box {
  background: #414bb2 !important;
}
</style>
