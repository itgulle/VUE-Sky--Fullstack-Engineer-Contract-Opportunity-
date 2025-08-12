<template>
  <div>
    <validation-provider
      v-slot="{ errors }"
      :rules="validationRules"
      :vid="componentId"
      :name="'Text'"
    >
      <ckeditor
        :editor="editor"
        :config="editorConfig"
        :model-value="editableValue"
        @input="update"
      />
      <div v-if="errors[0]">
        <span class="text-danger">{{ errors[0] }}</span>
      </div>

      <div class="count-remaining-section">
        <div :class="maxLengthObj.class">{{ maxLengthObj.text }}</div>
        <small class="text-secondary">This includes invisible formatting data</small>
      </div>
    </validation-provider>
  </div>
</template>

<script>
import ValidationProvider from "@/components/ValidationProvider.vue"
import {
  ClassicEditor,
  Heading,
  Bold,
  Essentials,
  Italic,
  Mention,
  Paragraph,
  Undo,
  List,
  Indent,
  Clipboard,
  Alignment
} from "ckeditor5"
import { Ckeditor } from "@ckeditor/ckeditor5-vue"
import "ckeditor5/ckeditor5.css"

export default {
  name: "RichTextEditor",
  components: { ValidationProvider, Ckeditor },
  props: {
    value: {
      type: String,
      required: false,
      default: ""
    }
  },
  data() {
    return {
      maxlength: 1000,
      editableValue: "",
      editor: ClassicEditor,
      editorConfig: {
        plugins: [
          Heading,
          Bold,
          Essentials,
          Italic,
          Mention,
          Paragraph,
          Undo,
          List,
          Indent,
          Clipboard,
          Alignment
        ],
        toolbar: [
          "heading",
          "undo",
          "redo",
          "essentials",
          "|",
          "bold",
          "italic",
          "|",
          "alignment",
          "|",
          "numberedList",
          "bulletedList",
          "indent",
          "outdent"
        ]
      }
    }
  },
  computed: {
    validationRules() {
      return "max:" + this.maxlength
    },
    // editableValue: {
    //   get() {
    //     return this.value
    //   },
    //   set(value) {
    //     this.$emit("input", value)
    //   }
    // },
    maxLengthObj() {
      let maxLength = this.maxlength
      let text = null
      let cssClass = ""
      if (maxLength !== null && !isNaN(maxLength)) {
        let curLen = this.editableValue ? this.editableValue.length : 0
        let remaining = maxLength - curLen
        let suffix = `(${remaining} remaining)`
        if (remaining < 0) {
          suffix = `(exceeded by ${remaining * -1})`
        }

        text = `${curLen} / ${maxLength} ${suffix}`
        if (remaining <= 20) {
          cssClass += " text-danger"
        }
      }
      return { text: text, class: cssClass }
    }
  },
  created() {
    this.editableValue = this.value ?? ""
  },
  methods: {
    update(value) {
      this.editableValue = value
      this.$emit("input", value)
    }
  }
}
</script>
<style scoped>
.count-remaining-section {
  margin-top: 0.5rem;
  float: right;
  text-align: right;
}
:deep(.ck-editor__editable) {
  min-height: 20rem;
  max-height: 20rem;
}
</style>
