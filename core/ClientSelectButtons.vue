<template>
  <div>
    <div v-if="clientsToDisplay.length > 1 && toggleFilterBy" class="row my-2">
      <div class="col">
        <span class="fw-bold">Filter by</span>
      </div>
    </div>
    <div
      v-if="clientsToDisplay.length > 1"
      class="btn-group"
      role="group"
      aria-label="Clients toggle button group"
    >
      <input
        v-if="showAll"
        id="btnAllClients"
        :ref="createRef('-1')"
        type="radio"
        class="btn-check"
        name="btnclients"
        autocomplete="off"
        checked
        @click="updateClientFilter('All', -1)"
      />
      <label v-if="showAll" class="btn btn-outline-primary px-3" for="btnAllClients"
        >All</label
      >
      <div v-if="clientsToDisplay.length === 1 && !showAll">
        <input
          id="btnClient0"
          :ref="createRef('0')"
          type="radio"
          class="btn-check"
          name="btnclients"
          autocomplete="off"
          checked
          @click="updateClientFilter(clientsToDisplay[0].fullname, 0)"
        />
        <label class="btn btn-outline-primary px-3" for="btnClient0">{{
          clientsToDisplay[0].fullname
        }}</label>
      </div>
      <div
        v-else
        class="btn-group client-name-btns"
        :class="[
          !showAll ? 'dont-show-all' : '',
          { 'select-button-is-invalid': highlightAsRequired }
        ]"
      >
        <div v-for="(client, index) in clientsToDisplay" :key="index">
          <input
            :id="`btnClient${index}`"
            :ref="createRef(index)"
            type="radio"
            class="btn-check"
            name="btnclients"
            autocomplete="off"
            :checked="!showAll && preselectedClient(client.id, index)"
            @click="updateClientFilter(client.fullname, index)"
          />
          <label
            class="btn px-3"
            :class="[
              index !== 0 ? 'label-display-left' : '',
              highlightAsRequired ? 'btn-outline-danger' : 'btn-outline-primary'
            ]"
            :for="`btnClient${index}`"
          >
            {{ client.fullname }}</label
          >
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters } from "vuex"
export default {
  props: {
    showAll: {
      type: Boolean,
      required: false,
      default: true
    },
    clientSource: {
      type: String,
      default: "client"
    },
    toggleFilterBy: {
      type: Boolean,
      required: false,
      default: true
    },
    selectedClient: {
      type: String,
      required: false,
      default: null
    },
    highlightAsRequired: {
      type: Boolean,
      default: () => false
    }
  },

  computed: {
    ...mapGetters({
      selectedClients: "clientFile/selectedClients",
      caseClients: "cases/currentCaseClients",
      case: "cases/currentCase",
      requirement: "requirements/selectedRequirement",
      selectedRequirementClients: "requirements/selectedRequirementClients"
    }),
    clientsToDisplay() {
      switch (this.clientSource) {
        case "client":
          return this.selectedClients
        case "case":
          return this.caseClients
        case "requirement":
          return this.selectedRequirementClients
        default:
          return this.selectedClients
      }
    }
  },
  methods: {
    //if you rename this method, you must also rename the callers
    //adding this for now, until larger refactor
    publicMethodUpdateClientFilter(index) {
      if ((index === -1 || index === "-1") && !this.showAll) {
        index = 0
      }

      if (isNaN(index)) {
        console.error("Argument out of range exception index is not a number")
      } else {
        try {
          let refStr = this.createRef(index)
          this.$refs[refStr].click()
        } catch (err) {
          console.error(err)
        }
      }
    },
    createRef(index) {
      return "btnClient" + index
    },
    updateClientFilter(name, index) {
      this.$emit("updateClientFilter", { name: name, index: index })
    },
    preselectedClient(id, index) {
      if (!this.selectedClient) {
        return index === 0
      }
      return id == this.selectedClient
    }
  }
}
</script>

<style scoped>
.client-name-btns div label {
  border-radius: 0px;
}
.label-display-left {
  border-left: 0px;
}
.dont-show-all div:first-child label {
  border-radius: 6px 0 0 6px;
}
.client-name-btns div:last-child label {
  border-radius: 0 6px 6px 0;
}
</style>
