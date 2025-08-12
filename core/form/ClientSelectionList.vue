<!-- This component is useful in any screen that involves adding client file records that can have multiple linked clients.
Clients can potentially be hidden in client file / fact find (if the advisor has access to a client through association but not that client's associates).
eg. Client A is open. Client B is associated to Client A and has a savings record with Client C. The advisor can see B's savings record but not Client C's name.
This component contains logic to handle displaying that and a warning message if needed. -->
<template>
  <div>
    <mb-form-input :guid="labelGuid" label-only></mb-form-input>
    <validation-provider
      ref="validationProvider"
      v-slot="{ errors, value }"
      :rules="{ required: true }"
      :name="validationId"
      :initial-value="selectedClients"
    >
      <multi-select-2
        :value="value"
        :options="clientSelectOptions"
        :name="validationId"
        :searchable="true"
        :disabled="readonly || hiddenOptionsExist"
        :highlight-as-required="!!errors[0]"
        @input="onClientSelected"
      ></multi-select-2>
      <span class="text-danger">{{ formatErrorMessage(errors[0]) }}</span>
    </validation-provider>
    <span v-show="!readonly && hiddenOptionsExist"
      >To edit this field, access a visible client & <br />
      enable the associated hidden client.</span
    >
  </div>
</template>

<script>
import ValidationProvider from "@/components/ValidationProvider.vue"
import { MultiSelect2 } from "@/plugins/components-plugin/entry.js"
import { mapGetters } from "vuex"

export default {
  components: { ValidationProvider, MultiSelect2 },
  props: {
    labelGuid: {
      type: String,
      required: true
    },
    linkedClients: {
      type: Array,
      required: true
    },
    clientSource: {
      type: String,
      default: "client"
    },
    excludedClients: {
      type: Array,
      default: () => []
    },
    readonly: {
      type: Boolean,
      default: () => false
    }
  },
  data() {
    return {
      selectedClients: [],
      clientSelectOptions: [],
      hiddenClientOptions: []
    }
  },
  computed: {
    ...mapGetters({
      storeClients: "clientFile/clients",
      caseClients: "cases/currentCaseClients",
      fieldByFormId: "datadictionary/fieldByFormID"
    }),
    clientsToDisplay() {
      switch (this.clientSource) {
        case "client":
          return this.storeClients
        case "case":
        case "requirement":
          return this.caseClients
        default:
          return this.storeClients
      }
    },
    hiddenOptionsExist() {
      return this.hiddenClientOptions.length > 0
    },
    labelText() {
      return this.fieldByFormId(this.labelGuid).label
    },
    // Using as meany unique identifiers as possible to avoid
    // repeated fields from syncing with each other.
    // VeeValidate v4 requires unique names per field as
    // it manages and drives the form state.
    validationId() {
      const vid = []

      if (this.componentId) {
        vid.push(this.componentId)
      }

      if (this.vidSuffix) {
        vid.push(this.vidSuffix)
      }

      return vid.join(":")
    }
  },
  created() {
    this.hiddenClientOptions = this.linkedClients
      .filter((id) => !this.clientsToDisplay.map((client) => client.id).includes(id))
      .map((hidden_client_id) => {
        return { value: hidden_client_id, text: "Other" }
      })
    this.clientSelectOptions = this.clientsToDisplay
      .reduce((options, client) => {
        options.push({ value: client.id, text: client.fullname })
        return options
      }, [])
      .concat(this.hiddenClientOptions)
    //Clients to be exluded
    if (this.excludedClients?.length > 0) {
      this.clientSelectOptions = this.clientSelectOptions.filter(
        (client) => !this.excludedClients.includes(client.value)
      )
    }
    this.selectedClients = this.clientSelectOptions.filter((client) =>
      this.linkedClients.includes(client.value)
    )
  },
  methods: {
    onClientSelected(options) {
      this.$refs.validationProvider?.handleChange(options)
      this.selectedClients = options
      this.$emit(
        "update:linkedClients",
        options.map((o) => o.value)
      )
    },
    formatErrorMessage(error = "") {
      if (this.validationId) {
        return error.replace(this.validationId, this.labelText)
      }
      return error
    }
  }
}
</script>
