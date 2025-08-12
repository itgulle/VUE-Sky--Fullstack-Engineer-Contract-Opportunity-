<template>
  <div class="dropdown">
    <button
      :id="`dropdownMenuButton${index}`"
      data-bs-toggle="dropdown"
      aria-expanded="false"
      class="btn dropdown-toggle"
      type="button"
    >
      &#9679;&#9679;&#9679;
    </button>

    <div class="dropdown-menu" :aria-labelledby="`dropdownMenuButton${index}`">
      <span class="dropdown-item"><b>Actions Menu</b></span>
      <div class="dropdown-divider"></div>
      <slot>
        <template v-for="(action, index) in actions" :key="index">
          <template v-if="!action.hidden">
            <button
              class="dropdown-item"
              :disabled="action.disabled"
              @click="$emit(action.event)"
            >
              {{ action.title }}
            </button>
          </template>
        </template>
      </slot>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue"
import type { PropType } from "vue"

interface Action {
  title: string
  event: string
  disabled: boolean
  hidden: boolean
}

export default defineComponent({
  name: "ActionMenu",
  props: {
    actions: {
      type: Array as PropType<Action[]>,
      required: true
    },
    index: {
      type: Number,
      default: 0
    }
  }
})
</script>
