<template>
  <div>
    <slot
      v-for="(data, index) in metaData"
      :name="`input(${data.name})`"
      :index="index"
      :input="flatten(data)"
    >
      <mb-form-input
        v-if="data.visible"
        :key="data._id"
        :value="model[data.key]"
        :guid="data.guid"
        :label="data.label"
        :readonly="data.readonly"
        :class="data.class"
        :label-class="data.labelClass"
        :input-class="data.inputClass"
        v-bind="data.props"
        @input="(e) => update(data.key, e)"
        @userupdated="(e) => data.userupdated(e)"
      ></mb-form-input>
    </slot>
  </div>
</template>

<script>
import { mapGetters } from "vuex"

export default {
  props: {
    value: {
      type: Object,
      required: true
    },
    inputs: {
      type: Array,
      required: true
    },
    label: {
      type: Boolean,
      default: () => false
    },
    readonly: {
      type: Boolean,
      default: () => false
    }
  },
  data() {
    return {
      metaData: []
    }
  },
  computed: {
    ...mapGetters({
      fieldByFormID: "datadictionary/fieldByFormID"
    }),
    model() {
      return this.value ?? {}
    }
  },
  created() {
    this.inputs.forEach((input, index) => {
      const temp = typeof input === "object" ? input : createTempInput(input)

      const {
        key,
        guid,
        name,
        label,
        readonly,
        visible,
        userupdated,
        ["class"]: mainClass,
        labelClass,
        inputClass,
        ...props
      } = temp

      const data = {
        key: key || this.fieldByFormID(guid)?.db_name,
        guid,
        name: name || guid,
        label: label ?? this.label,
        props: props
      }

      Object.defineProperty(data, "_id", {
        value: `${index}${Date.now()}`,
        enumerable: false
      })

      const hasKeyAndGuid = Boolean(data.key && data.guid)
      const visibleCallback = hasKeyAndGuid ? visible : null

      const visibleProperty = createVisibleProperty(visibleCallback, hasKeyAndGuid, this)

      Object.defineProperty(data, "visible", visibleProperty)

      const readonlyProperty = createReadonlyProperty(readonly, this)
      Object.defineProperty(data, "readonly", readonlyProperty)

      const userUpdatedProperty = createUserUpdatedProperty(userupdated, this)
      Object.defineProperty(data, "userupdated", userUpdatedProperty)

      const classProperty = createClassProperty(mainClass)
      Object.defineProperty(data, "class", classProperty)

      const labelClassProperty = createClassProperty(labelClass)
      Object.defineProperty(data, "labelClass", labelClassProperty)

      const inputClassProperty = createClassProperty(inputClass)
      Object.defineProperty(data, "inputClass", inputClassProperty)

      this.metaData.push(data)
    })

    function createTempInput(value) {
      const regexPattern = /^q-[0-9a-f]{8}/
      if (regexPattern.test(value)) {
        return { guid: value }
      }
      return { name: value }
    }

    function createVisibleProperty(visible, defaultValue, thisArg) {
      switch (typeof visible) {
        case "string":
          return {
            get: thisArg.$parent[visible],
            enumerable: true
          }
        case "function":
          return {
            get: visible,
            enumerable: true
          }
        default:
          return {
            value: defaultValue,
            enumerable: true
          }
      }
    }

    function createReadonlyProperty(readonly, thisArg) {
      switch (typeof readonly) {
        case "string":
          return {
            get: thisArg.$parent[readonly],
            enumerable: true
          }
        case "function":
          return {
            get: readonly,
            enumerable: true
          }
        case "boolean":
          return {
            value: readonly,
            enumerable: true
          }
        default:
          return {
            get: () => thisArg.readonly,
            enumerable: true
          }
      }
    }

    function createUserUpdatedProperty(callback, thisArg) {
      switch (typeof callback) {
        case "string":
          return {
            value: thisArg.$parent[callback],
            enumerable: true
          }
        case "function":
          return {
            value: callback,
            enumerable: true
          }
        default:
          return {
            value: (e) => thisArg.$emit("userupdated", e),
            enumerable: true
          }
      }
    }

    function createClassProperty(cssClass) {
      if (typeof cssClass === "function") {
        return {
          get: cssClass,
          enumerable: true
        }
      }
      return {
        value: cssClass,
        enumerable: true
      }
    }
  },
  methods: {
    update(key, value) {
      const newModel = { ...this.model, [key]: value }
      this.$emit("input", newModel)
    },
    flatten(metaData) {
      const { props, ...root } = metaData
      return { ...root, ...props }
    }
  }
}
</script>
