<template>
  <div class="vertical-menu">
    <div class="text-center">
      <span class="vertical-menu-datetime">{{ theDate }}</span>
    </div>
    <simplebar prefix="governanceMenu">
      <div id="sidebar-menu">
        <ul id="side-menu" class="metismenu list-unstyled">
          <li class="mm-active">
            <a href="#">
              <i class="bx bx-briefcase"></i>
              <span key="t-chart">Cases</span>
            </a>
            <ul>
              <li>
                {{ firm.name }}
                <ul>
                  <li>{{ caseData.caseInfo.ref }}</li>
                </ul>
              </li>
            </ul>
          </li>
        </ul>
      </div>
    </simplebar>

    <div class="navbar-brand-box place-bottom p-0">
      <router-link to="/" class="logo logo-light">
        <span class="logo-lg logo-collapseable-full">
          <img
            src="https://res.cloudinary.com/mortgage-brain-ltd/image/authenticated/s--Be4BQz78--/v1637769237/Mortgage%20Brain%20Company%20Logos/MB-Primary-Logo-_-Wide-W_padded_500px_WHT.png"
            alt=""
            height="50"
          />
        </span>
        <span class="logo-lg logo-collapseable-collapsed">
          <img
            src="https://res.cloudinary.com/mortgage-brain-ltd/image/authenticated/s--SOBrJZpA--/v1662714409/Mortgage%20Brain%20Company%20Logos/MB-Favicon_Padded-WO_nmzdk5.png"
            alt=""
            height="50"
          />
        </span>
      </router-link>
    </div>
  </div>
</template>
<script>
import { useStore } from "vuex"
import simplebar from "simplebar-vue"
import { format } from "date-fns"
import { headerDateFormat } from "@/global/modules/enum/constants"
import casePermissionsMixins from "@/global/modules/mixins/permissions/case-permissions-mixins"
import { ref, onMounted, onUnmounted, computed } from "vue"

export default {
  components: {
    simplebar
  },
  mixins: [casePermissionsMixins],
  setup() {
    const theDate = ref("")
    const interval = ref(0)
    const store = useStore()

    onMounted(() => {
      interval.value = window.setInterval(updateDate, 1000)
    })

    onUnmounted(() => {
      window.clearInterval(interval.value)
    })

    function updateDate() {
      theDate.value = format(new Date(), headerDateFormat)
    }

    return {
      theDate,
      interval,
      firm: computed(() => store.getters["governance/getFirm"]),
      caseData: computed(() => store.getters["cases/currentCaseMockData"])
    }
  }
}
</script>
<style>
#sidebar-menu {
  height: 100vh;
}
</style>

<style scoped lang="scss">
.place-bottom {
  bottom: 0;
  position: fixed;
  right: 0;
  left: 0;
}

.navbar-brand-box img {
  height: 40px;
  margin-top: 1.5rem;
  margin-bottom: 1.5rem;
}

.vertical-menu-datetime {
  color: #fff;
  font-size: 0.85em;
}
.logo-collapseable-collapsed {
  display: none;
}
.vertical-collapsed .logo-collapseable-collapsed {
  display: block;
}
.vertical-collapsed .logo-collapseable-full {
  display: none;
}
.vertical-menu,
.navbar-brand-box {
  background: #414bb2 !important;
}
.mm-active {
  background-color: #414bb2;

  a {
    background-color: #414bb2 !important;
  }

  ul {
    background-color: #414bb2 !important;
  }
}

#sidebar-menu ul li i {
  display: inline-block;
  min-width: 1.75rem;
  padding-bottom: 0.125em;
  font-size: 1.25rem;
  line-height: 1.40625rem;
  vertical-align: middle;
}
</style>
