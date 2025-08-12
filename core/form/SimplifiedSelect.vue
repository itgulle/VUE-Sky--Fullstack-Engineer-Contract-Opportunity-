<template>
  <div>
    <div class="form-input-group">
      <label class="col-form-label fw-light" :for="componentId"
        >{{ computedLabel }}
        <span v-if="required" class="text-danger"> *</span>
      </label>
      <div class="d-flex flex-column">
        <validation-provider :rules="{ required: required }">
          <mb-select-input
            :id="componentId"
            :value="value"
            class="flex-grow-0 flex-shrink-0"
            :field="{
              data_type: dataType,
              validation: {
                required: required
              },
              input_config: {
                placeholder: 'Select a value'
              },
              select_values: selectOptions
            }"
            @input="(e) => $emit('input', e)"
          />
        </validation-provider>
      </div>
    </div>
  </div>
</template>
<script>
import { MbSelectInput } from "@/plugins/components-plugin/entry.js"
import ValidationProvider from "@/components/ValidationProvider.vue"
import { mapGetters } from "vuex"
export default {
  components: { MbSelectInput, ValidationProvider },
  props: {
    value: {
      type: [String, Number],
      required: false,
      default: () => null
    },
    label: {
      type: String,
      required: false,
      default: null
    },
    labelIsGuid: {
      type: Boolean,
      required: false,
      default: false
    },
    selectOptions: {
      type: Array,
      required: true
    },
    required: {
      type: Boolean,
      required: false,
      default: false
    },
    dataType: {
      type: String,
      required: true
    }
  },
  computed: {
    ...mapGetters({
      fieldByFormID: "datadictionary/fieldByFormID"
    }),
    computedLabel() {
      if (!this.labelIsGuid) return this.label ?? ""
      return this.fieldByFormID(this.label)?.label ?? ""
    }
  }
}
</script>

<style></style>
