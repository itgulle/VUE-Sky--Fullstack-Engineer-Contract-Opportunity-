<template>
  <div class="w-100">
    <div class="scroller scroller-left float-start" @click="scrollToLeft()">
      <div class="d-flex" :style="{ height: wrapperHeight }">
        <i class="bx bxs-left-arrow align-self-center"></i>
      </div>
    </div>
    <div class="scroller scroller-right float-end" @click="scrollToRight()">
      <div class="d-flex" :style="{ height: wrapperHeight }">
        <i class="bx bxs-right-arrow align-self-center"></i>
      </div>
    </div>
    <div
      class="wrapper-nav"
      :style="{ height: wrapperHeight }"
      @click="scollActiveTabIntoView()"
    >
      <slot nav-class="scrollable-list"></slot>
    </div>
  </div>
</template>

<script>
import { scrollableTabs } from "@/global/modules/scrollableTabs"

export default {
  data() {
    return {
      wrapperHeight: "36px"
    }
  },
  mounted() {
    window.addEventListener("resize", this.scrollTabsIntoView)
  },
  beforeUnmount() {
    window.removeEventListener("resize", this.scrollTabsIntoView)
  },
  methods: {
    async initializeTabs(navTabs) {
      if (navTabs) {
        this.wrapperHeight = `${navTabs.clientHeight}`

        await this.$nextTick()

        scrollableTabs.initialize()
        this.scollActiveTabIntoView()
      }
    },
    async scrollTabsIntoView() {
      clearTimeout(this.resizeTimer)
      await this.$nextTick()
      this.resizeTimer = setTimeout(() => {
        scrollableTabs.scrollTabsIntoView()
        this.scollActiveTabIntoView()
      }, 250)
    },
    scrollToLeft() {
      scrollableTabs.scrollLeft()
    },
    scrollToRight() {
      scrollableTabs.scrollRight()
    },
    scollActiveTabIntoView() {
      setTimeout(() => {
        scrollableTabs.scrollActiveTabIntoView()
      }, 500)
    }
  }
}
</script>

<style>
.wrapper-nav {
  display: flex;
  position: relative;
  margin: 0 auto;
  overflow: hidden;
  flex-wrap: nowrap;
}

.wrapper-nav .scrollable-list {
  position: absolute;
  left: 0px;
  top: 0px;
  min-width: 3500px;
  margin-top: 0px;
  flex-wrap: nowrap;
  border-bottom: none;
}

.wrapper-nav .scrollable-list .nav-item {
  white-space: nowrap;
}

.scroller {
  text-align: center;
  cursor: pointer;
  display: none;
  white-space: no-wrap;
  vertical-align: middle;
  font-size: 18px;
}

.scroller-left {
  padding: 0 12px 0 0;
}

.scroller-right {
  padding: 0 0 0 12px;
}
</style>
