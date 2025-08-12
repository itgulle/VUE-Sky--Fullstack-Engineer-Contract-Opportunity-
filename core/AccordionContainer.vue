<template>
  <div>
    <div class="mb-2">
      <a href="javascript:void(0)" @click.prevent="expandAll(true)">Expand all</a>
      /
      <a href="javascript:void(0)" @click.prevent="expandAll(false)">Collapse all</a>
    </div>
    <slot></slot>
  </div>
</template>

<script lang="ts" setup>
import { inject, onBeforeMount, onBeforeUnmount, provide, useId } from "vue"
import { Accordion } from "./AccordionItem.vue"

export type Container = {
  id: string
  register: (subscriber: any, kind: string) => void
  unregister: (id: string, kind: string) => void
  expandAll: (value: boolean) => void
}

const id = useId()
const containers: Container[] = []
let accordions: Record<string, Accordion> = {}

const register = (subscriber: any, kind = "accordion") => {
  if (kind === "container") {
    containers.push(subscriber)
    return
  }

  accordions = { ...accordions, [subscriber.id]: subscriber }
}

const unregister = (id: string, kind = "accordion") => {
  if (kind === "container") {
    const index = containers.findIndex((c) => c.id === id)

    if (index > -1) {
      containers.splice(index, 1)
    }
    return
  }

  delete accordions[id]
}

provide("$accordion_container", { register, unregister })

const expandAll = (value: boolean) => {
  Object.values(accordions).forEach((accordion) => accordion.expand(value))

  containers.forEach((container) => {
    container.expandAll(value)
  })
}

const outerContainer = <Container>inject("$accordion_container")

onBeforeMount(() => {
  outerContainer?.register({ id, expandAll }, "container")
})

onBeforeUnmount(() => {
  outerContainer?.unregister(id, "container")
})

defineExpose({
  expandAll
})
</script>
