<template>
  <div>
    <mb-form-input
      v-model="selectedOptions"
      :vid-suffix="'d-2'"
      :guid="guid"
      :selectoptions="clientSelectList"
      :readonly="readonly"
      :required="true"
      label-only
    ></mb-form-input>
    <validation-provider
      ref="validationProvider"
      v-slot="{ errors, value }"
      :rules="{
        required: true,
        any_of: [individualIds, 'An individual must be included.']
      }"
      :name="validationId"
      :initial-value="selectedOptions"
    >
      <multi-select-2
        :value="value"
        :options="clientSelectList"
        :name="validationId"
        :searchable="true"
        :disabled="readonly"
        :highlight-as-required="highlightAsRequired || !!errors[0]"
        @input="onClientSelected"
      ></multi-select-2>
      <span class="text-danger">{{ formatErrorMessage(errors[0]) }}</span>
    </validation-provider>
  </div>
</template>

<script>
import ValidationProvider from "@/components/ValidationProvider.vue"
import { MultiSelect2 } from "@/plugins/components-plugin/entry.js"
import { mapGetters } from "vuex"

export default {
  components: { ValidationProvider, MultiSelect2 },
  props: {
    guid: {
      type: String,
      required: true
    },
    companySelectOptions: {
      type: Array,
      default: () => []
    },
    individualSelectOptions: {
      type: Array,
      default: () => []
    },
    selectedCompanies: {
      type: Array,
      default: () => []
    },
    selectedIndividuals: {
      type: Array,
      default: () => []
    },
    readonly: {
      type: Boolean,
      default: () => false
    },
    highlightAsRequired: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      selectedOptions: []
    }
  },
  computed: {
    ...mapGetters({
      fieldByFormId: "datadictionary/fieldByFormID"
    }),
    individualIds() {
      return this.individualSelectOptions.reduce((a, o) => (a.push(o.value), a), [])
    },
    companyIds() {
      return this.companySelectOptions.reduce((a, o) => (a.push(o.value), a), [])
    },
    clientSelectList() {
      var list = []
      list.push(...this.individualSelectOptions)
      list.push(...this.companySelectOptions)
      return list
    },
    labelText() {
      return this.fieldByFormId(this.guid).label
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
  watch: {
    selectedIndividuals: {
      handler() {
        this.syncSelectedOptions()
      },
      immediate: true
    },
    selectedCompanies: {
      handler() {
        this.syncSelectedOptions()
      },
      immediate: true
    }
  },
  methods: {
    onClientSelected(options) {
      this.$refs.validationProvider?.handleChange(options)
      if (options?.length !== this.selectedOptions.length) {
        this.$emit(
          "update:selectedCompanies",
          options.filter((o) => this.companyIds.includes(o.value)).map((o) => o.value)
        )
        this.$emit(
          "update:selectedIndividuals",
          options.filter((o) => this.individualIds.includes(o.value)).map((o) => o.value)
        )
      }
    },
    formatErrorMessage(error = "") {
      if (this.validationId) {
        return error.replace(this.validationId, this.labelText)
      }
      return error
    },
    syncSelectedOptions() {
      this.selectedOptions.splice(0)

      this.selectedOptions.push(
        ...this.individualSelectOptions.filter((x) =>
          this.selectedIndividuals.includes(x.value)
        )
      )
      this.selectedOptions.push(
        ...this.companySelectOptions.filter((x) =>
          this.selectedCompanies?.includes(x.value)
        )
      )

      this.$refs.validationProvider?.handleChange(this.selectedOptions)
    }
  }
}
</script>
