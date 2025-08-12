<template>
  <div ref="el" class="dropdown d-none d-lg-inline-block">
    <button
      type="button"
      class="btn header-item noti-icon waves-effect"
      data-bs-toggle="dropdown"
      aria-haspopup="true"
      aria-expanded="false"
    >
      <i class="bx bx-calculator"></i>
    </button>
    <div id="calculatorsMenu" class="dropdown-menu dropdown-menu-end">
      <mortgage-payment-calculator v-show="mpcShow" />
      <div v-show="menuOptionsShow">
        <a
          href="#"
          class="dropdown-item notify-item language"
          data-lang="en"
          @click.stop="toggle"
        >
          <i class="bx bx-calculator"></i>
          <span class="p-2">Mortgage Payment Calculator</span>
        </a>
        <!-- temp removal for mvp -->
        <!--<a
          href="javascript:void(0);"
          class="dropdown-item notify-item language"
          data-lang="en"
        >
          <i class="bx bx-calculator"></i>
          <span class="p-2">Buy to Let Calculator</span>
        </a>
        <a
          href="javascript:void(0);"
          class="dropdown-item notify-item language"
          data-lang="en"
        >
          <i class="bx bx-calculator"></i>
          <span class="p-2">Debt Consolidation Calculator</span>
        </a>
        <br />
        <a
          href="javascript:void(0);"
          class="dropdown-item notify-item language"
          data-lang="en"
        >
          <i class="bx bx-calculator"></i>
          <span class="p-2">Loan &amp; HP Calculator</span>
        </a>
        <a
          href="javascript:void(0);"
          class="dropdown-item notify-item language"
          data-lang="en"
        >
          <i class="bx bx-calculator"></i>
          <span class="p-2">Overpayment Calculator</span>
        </a>
        <a
          href="javascript:void(0);"
          class="dropdown-item notify-item language"
          data-lang="en"
        >
          <i class="bx bx-calculator"></i>
          <span class="p-2">Mortgage Payment Schedule</span>
        </a>-->
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from "vue"
import MortgagePaymentCalculator from "./calculators/MortgagePaymentCalculator.vue"

const mpcShow = ref(false)
const menuOptionsShow = ref(true)
const el = ref<HTMLDivElement | null>(null)

onMounted(() => {
  // to keep the calculator open while clicks occur.
  const calculatorsMenu = document.getElementById("calculatorsMenu")
  if (!calculatorsMenu) {
    return
  }

  calculatorsMenu.addEventListener("click", (event) => {
    event.stopPropagation()
  })

  el.value?.addEventListener("hide.bs.dropdown", () => {
    mpcShow.value = false
    menuOptionsShow.value = true
  })
})

const toggle = () => {
  console.log(mpcShow.value)
  mpcShow.value = true
  menuOptionsShow.value = false
}
</script>

<style scoped>
.dropdown-menu {
  min-width: 340px;
}
</style>
