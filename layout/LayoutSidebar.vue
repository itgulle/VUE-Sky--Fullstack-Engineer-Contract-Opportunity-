<template>
  <!-- TODO: Move this to a json config or something -->
  <div class="vertical-menu">
    <div class="text-center">
      <span class="vertical-menu-datetime">{{ theDate }}</span>
    </div>
    <simplebar ref="currentMenu" class="h-100">
      <div id="sidebar-menu">
        <ul id="side-menu" class="metismenu list-unstyled">
          <router-link
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'dashboard' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" @click="navigate">
                <i class="bx bx-home-circle"></i>
                <span key="t-dashboards">Dashboard</span>
              </a>
            </li>
          </router-link>
          <router-link
            v-slot="{
              href: hrefClient,
              navigate: navigateClient,
              isActive: isActiveClient
            }"
            :to="{ name: 'clients' }"
            custom
          >
            <li :class="[(isActiveClient || clientsPageActive) && 'mm-active']">
              <a
                :href="hrefClient"
                class="waves-effect is-parent"
                @click="navigateClient"
              >
                <div class="d-flex">
                  <div style="min-width: 1.75rem">
                    <svg
                      xmlns="http://www.w3.org/2000/svg"
                      width="20"
                      height="20"
                      fill="currentColor"
                      class="bi bi-people-fill"
                      viewBox="0 0 16 16"
                    >
                      <path
                        d="M7 14s-1 0-1-1 1-4 5-4 5 3 5 4-1 1-1 1H7zm4-6a3 3 0 1 0 0-6 3 3 0 0 0 0 6z"
                      />
                      <path
                        fill-rule="evenodd"
                        d="M5.216 14A2.238 2.238 0 0 1 5 13c0-1.355.68-2.75 1.936-3.72A6.325 6.325 0 0 0 5 9c-4 0-5 3-5 4s1 1 1 1h4.216z"
                      />
                      <path d="M4.5 8a2.5 2.5 0 1 0 0-5 2.5 2.5 0 0 0 0 5z" />
                    </svg>
                  </div>

                  <span key="t-clients">Clients</span>
                </div>
              </a>
              <ul
                v-if="clientsMenuOpen"
                id="client-menu"
                class="sub-menu"
                aria-expanded="false"
              >
                <router-link
                  v-slot="{ href, navigate, isActive }"
                  :to="{
                    name: 'client-overview',
                    params: { id: currentClientId }
                  }"
                  custom
                >
                  <li :class="[(isActive || clientFileActive) && 'mm-active']">
                    <a :href="href" @click="navigate"> Client File </a>
                  </li>
                </router-link>

                <router-link
                  v-slot="{ href, navigate, isActive }"
                  :to="{
                    name: 'client-cases',
                    params: { id: currentClientId }
                  }"
                  custom
                >
                  <li :class="[(isActive || clientCasesActive) && 'mm-active']">
                    <a :href="href" @click="navigate">Cases</a>
                  </li>
                </router-link>

                <!-- <router-link
                  v-if="!hidden['client-reviews']"
                  v-slot="{ href, navigate, isActive }"
                  :to="{
                    name: 'client-reviews',
                    params: { id: currentClientId }
                  }"
                  custom
                >
                  <li :class="[isActive && 'mm-active']">
                    <a :href="href" @click="navigate"> Reviews</a>
                  </li>
                </router-link> -->
                <!-- <li>
                  <router-link :to="{ name: 'client-docs' }">
                    Documents
                  </router-link>
                </li> -->
                <!-- <li v-if="!hidden['client-insights']">
                  <router-link :to="{ name: 'client-insights' }">
                    Insights
                  </router-link>
                </li>
                <li v-if="!hidden['client-fees']">
                  <router-link :to="{ name: 'client-fees' }">
                    Commissions &amp; Fees
                  </router-link>
                </li>
                <li v-if="!hidden['client-notes']">
                  <router-link :to="{ name: 'client-notes' }"
                    >Notes</router-link
                  >
                </li> -->
                <li>
                  <router-link
                    :to="{
                      name: 'client-portal',
                      params: {
                        id: currentClientId
                      }
                    }"
                  >
                    Client Portal
                  </router-link>
                </li>
                <li v-if="!hidden['client-debug']">
                  <router-link
                    :to="{
                      name: 'client-debug',
                      params: { id: currentClientId }
                    }"
                  >
                    Client's Debug
                  </router-link>
                </li>
                <!-- <li v-if="!hidden['client-valuations']">
                  <router-link :to="{ name: 'client-valuations' }">
                    Valuations
                  </router-link>
                </li> -->
              </ul>
            </li>
          </router-link>
          <router-link
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'opportunities' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" class="waves-effect" @click="navigate">
                <i class="bx bx-chat"></i>
                <span key="t-chat">Opportunities</span>
              </a>
            </li>
          </router-link>
          <router-link
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'referrals-overview' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" class="waves-effect" @click="navigate">
                <i class="bx bx-chat"></i>
                <span key="t-chat">Referrals</span>
              </a>
            </li>
          </router-link>
          <!-- <router-link -->
          <!--   v-if="!hidden['document-template-list']" -->
          <!--   v-slot="{ href, navigate, isActive }" -->
          <!--   :to="{ name: 'document-template-list' }" -->
          <!--   custom -->
          <!-- > -->
          <!--   <li :class="[isActive && 'mm-active']"> -->
          <!--     <a :href="href" class="waves-effect" @click="navigate"> -->
          <!--       <i class="bx bx-file"></i> -->
          <!--       <span key="t-chat">Documents</span> -->
          <!--     </a> -->
          <!--   </li> -->
          <!-- </router-link> -->
          <router-link
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'reporting' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" class="waves-effect" @click="navigate">
                <i class="bx bx-file"></i>
                <span key="t-chat">Reporting</span>
              </a>
            </li>
          </router-link>
          <router-link
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'insights' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" class="waves-effect" @click="navigate">
                <i class="bx bx-box"></i>
                <span key="t-chat">Insights</span>
              </a>
            </li>
          </router-link>
          <li>
            <a class="waves-effect">
              <i class="bx bx-box"></i>
              <span key="t-chat">Business Processing</span>
            </a>
            <ul class="sub-menu" aria-expanded="true">
              <router-link
                v-slot="{ href, navigate, isActive }"
                :to="{
                  name: 'all-tasks'
                }"
                custom
              >
                <li :class="[isActive && 'mm-active']">
                  <a :href="href" @click="navigate">All Tasks</a>
                </li>
              </router-link>
              <router-link
                v-slot="{ href, navigate, isActive }"
                :to="{
                  name: 'cases-processing'
                }"
                custom
              >
                <li :class="[isActive && 'mm-active']">
                  <a :href="href" @click="navigate">Cases</a>
                </li>
              </router-link>
              <router-link
                v-slot="{ href, navigate, isActive }"
                :to="{
                  name: 'application-processing'
                }"
                custom
              >
                <li :class="[isActive && 'mm-active']">
                  <a :href="href" @click="navigate">Applications</a>
                </li>
              </router-link>
              <router-link
                v-slot="{ href, navigate, isActive }"
                :to="{
                  name: 'compliance-reviews'
                }"
                custom
              >
                <li :class="[isActive && 'mm-active']">
                  <a :href="href" @click="navigate">Compliance Reviews</a>
                </li>
              </router-link>
            </ul>
          </li>
          <li>
            <a class="waves-effect">
              <i class="bx bx-box"></i>
              <span key="t-chat">Commissions Processing</span>
            </a>
            <ul class="sub-menu" aria-expanded="true">
              <router-link
                v-slot="{ href, navigate, isActive }"
                :to="{
                  name: 'commissions-written'
                }"
                custom
              >
                <li :class="[isActive && 'mm-active']">
                  <a :href="href" @click="navigate">Written Pipeline</a>
                </li>
              </router-link>
              <router-link
                v-slot="{ href, navigate, isActive }"
                :to="{
                  name: 'commissions-due'
                }"
                custom
              >
                <li :class="[isActive && 'mm-active']">
                  <a :href="href" @click="navigate">Commission Due</a>
                </li>
              </router-link>
              <router-link
                v-slot="{ href, navigate, isActive }"
                :to="{
                  name: 'commissions-received'
                }"
                custom
              >
                <li :class="[isActive && 'mm-active']">
                  <a :href="href" @click="navigate">Commission Received</a>
                </li>
              </router-link>
              <router-link
                v-slot="{ href, navigate, isActive }"
                :to="{
                  name: 'commissions-reconciled'
                }"
                custom
              >
                <li :class="[isActive && 'mm-active']">
                  <a :href="href" @click="navigate">Commission Reconciled</a>
                </li>
              </router-link>
            </ul>
          </li>
          <!-- <router-link
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'case-admin' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" class="waves-effect" @click="navigate">
                <i class="bx bx-briefcase"></i>
                <span key="t-chat">Case Admin</span>
              </a>
            </li>
          </router-link> -->
          <router-link
            v-if="canViewCompliance"
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'compliances' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" class="waves-effect" @click="navigate">
                <i class="bx bx-task"></i>
                <span key="t-chat">Compliance</span>
              </a>
            </li>
          </router-link>
          <!-- <router-link
            v-if="!hidden['commissions']"
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'commissions' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" class="waves-effect" @click="navigate">
                <i class="bx bx-money"></i>
                <span key="t-chat">Commissions</span>
              </a>
            </li>
          </router-link> -->
          <router-link
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'addressbook' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" class="waves-effect" @click="navigate">
                <i class="bx bxs-user-detail"></i>
                <span key="t-chart">Address Book</span>
              </a>
            </li>
          </router-link>
          <!--  <router-link
            v-if="!hidden['diary']"
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'diary' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" class="waves-effect" @click="navigate">
                <i class="bx bx-calendar"></i>
                <span key="t-chat">Diary</span>
              </a>
            </li>
          </router-link> -->
          <router-link
            v-if="!hidden['dctionary']"
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'dctionary' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" class="waves-effect" @click="navigate">
                <i class="bx bx-book"></i>
                <span key="t-chat">Data Dictionary</span>
              </a>
            </li>
          </router-link>
          <router-link
            v-if="!hidden['development']"
            v-slot="{ href, navigate, isActive }"
            :to="{ name: 'development' }"
            custom
          >
            <li :class="[isActive && 'mm-active']">
              <a :href="href" class="waves-effect" @click="navigate">
                <i class="bx bx-laptop"></i>
                <span key="t-chat">Development</span>
              </a>
              <ul class="sub-menu" aria-expanded="false">
                <li>
                  <router-link :to="{ name: 'dev-notes' }">Notes</router-link>
                </li>
                <li>
                  <router-link :to="{ name: 'app-health' }"> App Health </router-link>
                </li>
                <li>
                  <router-link :to="{ name: 'secure-messaging-poc' }">
                    Secure Messaging POC
                  </router-link>
                </li>
                <li>
                  <router-link :to="{ name: 'route-without-clientid-demo' }">
                    Route to case
                  </router-link>
                </li>
                <li>
                  <router-link :to="{ name: 'query-builder-list' }"
                    >Query Builder List</router-link
                  >
                </li>
                <li>
                  <router-link :to="{ name: 'scrollable-tabs' }"
                    >Scrollable Tabs</router-link
                  >
                </li>
                <li>
                  <router-link :to="{ name: 'development-user-info' }">
                    Current User Info
                  </router-link>
                </li>
                <li>
                  <router-link :to="{ name: 'style-guide' }">Style Guide</router-link>
                </li>
                <li>
                  <router-link :to="{ name: 'development-model-demo' }">
                    Model Dialog Demo
                  </router-link>
                </li>
                <li>
                  <router-link :to="{ name: 'development-form-events' }">
                    Form Events Demo
                  </router-link>
                </li>
                <li>
                  <router-link :to="{ name: 'highlight-as-required' }"
                    >Highlight As Required</router-link
                  >
                </li>
                <li>
                  <router-link :to="{ name: 'props-down-events-up' }"
                    >Props down Events up</router-link
                  >
                </li>
                <li>
                  <router-link :to="{ name: 'dd-driven-form-inputs' }"
                    >DD driven form inputs</router-link
                  >
                </li>
              </ul>
            </li>
          </router-link>
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
import { mapGetters } from "vuex"
import simplebar from "simplebar-vue"
import { format } from "date-fns"
import { headerDateFormat } from "@/global/modules/enum/constants"
import compliancePermissionsMixins from "@/global/modules/mixins/permissions/compliance-permissions-mixins"

export default {
  components: {
    simplebar
  },
  mixins: [compliancePermissionsMixins],
  data() {
    return {
      settings: {
        minScrollbarLength: 60
      },
      permissions: {
        task_compliance_cta_manage: { success: false }
      },
      theDate: "",
      interval: 0
    }
  },
  computed: {
    ...mapGetters({
      currentClientId: "clients/currentClientId"
    }),
    canViewCompliance() {
      return this.permissions.task_compliance_cta_manage.success
    },
    hidden() {
      let shouldHide = import.meta.env.PROD
      return {
        development: shouldHide,
        dctionary: shouldHide,
        opportunities: shouldHide,
        "client-debug": shouldHide,
        "client-valuations": shouldHide,
        "client-insights": shouldHide,
        "client-reviews": shouldHide,
        "client-fees": shouldHide,
        "client-notes": shouldHide,
        "client-portal": shouldHide,
        //commissions: shouldHide
        diary: shouldHide,
        "document-template-list": shouldHide
      }
    },
    clientFileActive() {
      return this.$store.state.clients.clientFileActive
    },
    clientsMenuOpen() {
      return (
        (this.clientFileActive ||
          this.clientCasesActive ||
          this.clientPortalNavActive ||
          this.clientDebugNavActive) &&
        Boolean(this.currentClientId)
      )
    },
    clientsPageActive() {
      return Boolean(this.currentClientId)
    },
    clientCasesActive() {
      return this.$store.state.clients.clientCasesActive
    },
    clientPortalNavActive() {
      return this.$store.state.clients.clientPortalNavActive
    },
    clientDebugNavActive() {
      return this.$store.state.clients.clientDebugNavActive
    }
  },
  created() {
    this.updateDate()
  },
  async mounted() {
    this.interval = setInterval(this.updateDate, 1000)

    const result = await this.canCtaManageComplianceReviewTask()
    this.permissions.task_compliance_cta_manage.success = result.success
  },
  beforeUnmount() {
    clearInterval(this.interval)
  },
  methods: {
    updateDate() {
      this.theDate = format(new Date(), headerDateFormat)
    }
  }
}
</script>
<style>
#sidebar-menu {
  height: 1200px;
}
</style>

<style scoped>
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
  color: #74788d;
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
</style>
