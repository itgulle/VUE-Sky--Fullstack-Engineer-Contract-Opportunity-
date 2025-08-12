<template>
  <div v-if="!config.isEmpty" class="form-input-group">
    <label :for="inputIdentifier" :class="labelClass">
      {{ config.label
      }}<span v-if="config.validation.required" class="text-danger"> *</span>
    </label>
    <validation-provider
      ref="validationProvider"
      v-slot="{ errors, value }"
      :rules="validationRules"
      :name="validationId"
      :initial-value="selectedOptions"
    >
      <multi-select-2
        :id="inputIdentifier"
        :value="value"
        :options="options"
        :placeholder="attributes.placeholder"
        :name="validationId"
        :searchable="searchable"
        :disabled="disabled"
        :pre-select="preSelect"
        :guid="guid"
        :display="display"
        :style="attributes.style"
        :class="attributes.class"
        :highlight-as-required="highlightAsRequired"
        @input="onInput"
      ></multi-select-2>
      <span class="text-danger">{{ formatErrorMessage(errors[0]) }}</span>
    </validation-provider>
  </div>
</template>

<script>
import ValidationProvider from "@/components/ValidationProvider.vue"
import { mapGetters } from "vuex"
import { MultiSelect2 } from "@/plugins/components-plugin/entry.js"
import { functionService } from "@/global/modules/helpers"

export default {
  components: { ValidationProvider, MultiSelect2 },
  props: {
    options: {
      type: Array,
      required: false,
      default: null
    },
    name: {
      type: String,
      required: false,
      default: () => "text"
    },
    disabled: {
      type: Boolean,
      default: false
    },
    searchable: {
      type: Boolean,
      default: false
    },
    guid: {
      type: String,
      default: null,
      required: true
    },
    display: {
      type: Array,
      default: null,
      required: false
    },
    preSelect: {
      type: Boolean,
      default: true,
      required: false
    },
    value: {
      type: Array,
      default: () => null
    },
    highlightAsRequired: {
      type: Boolean,
      default: false
    },
    vidSuffix: {
      type: String,
      default: () => null
    },
    attrs: {
      type: Object,
      default: () => null
    },
    labelClass: {
      type: String,
      default: "col-form-label fw-light"
    }
  },
  data() {
    return {
      config: null,
      attributes: {
        placeholder: "Select all that apply...",
        style: ""
      },
      selectedOptions: []
    }
  },
  computed: {
    ...mapGetters({
      dictionary: "datadictionary/fieldByFormID"
    }),
    inputIdentifier() {
      return `${Date.now()}-${this.formId}`
    },
    validationRules() {
      const rules = []
      if (this.config.validation.required ?? false) {
        rules.push("required")
      }

      if (this.config.validation.rules) {
        rules.push(this.config.validation.rules)
      }

      return rules.join("|")
    },
    // Using as meany unique identifiers as possible to avoid
    // repeated fields from syncing with each other.
    // VeeValidate v4 requires unique names per field as
    // it manages and drives the form state.
    validationId() {
      const vid = []

      if (this.labelGuid) {
        vid.push(this.labelGuid)
      }

      if (this.vidSuffix) {
        vid.push(this.config.vidSuffix)
      }

      if (this.$vnode.key) {
        vid.push(this.$vnode.key)
      }

      if (this.componentId) {
        vid.push(this.componentId)
      }

      return vid.join(":")
    }
  },
  created() {
    this.config = this.dictionary(this.guid)
    this.selectedOptions = this.options.filter((option) =>
      this.value.some((value) => value.value === option.value)
    )
  },
  beforeMount() {
    this.attributes = Object.assign(this.attributes, this.config.input_config)
  },
  mounted() {
    const minWidth = functionService.calculateMinWidthForSelectInput(
      this.options,
      this.name
    )
    this.attributes.style = `min-width: ${minWidth}px`
    this.attributes = Object.assign(this.attributes, this.attrs)
  },
  methods: {
    onInput(options) {
      this.$refs.validationProvider?.handleChange(options)
      this.selectedOptions = options
      this.$emit("input", options)
    },
    formatErrorMessage(error = "") {
      if (this.validationId) {
        return error.replace(this.validationId, this.config.label)
      }
      return error
    }
  }
}
</script>
