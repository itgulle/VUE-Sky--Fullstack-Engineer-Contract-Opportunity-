<template>
  <div :id="`accordion-${id}`" class="accordion">
    <div class="accordion-item">
      <div class="accordion-header">
        <button
          :ref="`accordion-${id}`"
          class="accordion-button collapsed"
          type="button"
          data-bs-toggle="collapse"
          :data-bs-target="`#accordionCollapse-${id}`"
          aria-expanded="true"
          :aria-controls="`accordionCollapse-${id}`"
        >
          <slot name="header"></slot>
        </button>
      </div>
      <div :id="`accordionCollapse-${id}`" class="accordion-collapse collapsed collapse">
        <div class="accordion-body">
          <slot name="body"></slot>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { inject, onBeforeMount, onBeforeUnmount, useId, useTemplateRef } from "vue"
import { Container } from "./AccordionContainer.vue"

export type Accordion = {
  id: string
  expand: (value: boolean) => void
}

const id = useId()

const accordionTemplateRef = useTemplateRef<HTMLButtonElement>(`accordion-${id}`)

const expand = (value: boolean) => {
  if (accordionTemplateRef.value.classList.contains("collapsed") === value) {
    accordionTemplateRef.value.click()
  }
}

const container = <Container>inject("$accordion_container")

onBeforeMount(() => {
  container?.register({ id, expand }, "accordion")
})

onBeforeUnmount(() => {
  container?.unregister(id, "accordion")
})

defineExpose({
  expand
})
</script>
