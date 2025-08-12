<template>
  <div
    class="page-content"
    :class="{
      'page-content--warning': showWarning
    }"
  >
    <div v-if="showWarning" class="page-content__warning-wrapper">
      <div class="flex-grow-1 flex-shrink-1">
        <i class="mdi mdi-camera-wireless-outline align-middle lh-1 fs-4 me-2" />
        <span class="align-middle lh-1"><slot name="warning-left"></slot></span>
      </div>
      <div class="flex-grow-0 flex-shrink-0">
        <slot name="warning-right"></slot>
      </div>
    </div>
    <div class="page-content__container container-fluid">
      <div class="d-flex justify-content-between">
        <div>
          <div v-if="breadcrumb && !canGovernCase" class="row">
            <div class="d-flex align-items-center justify-content-between">
              <p class="font-size-13 text-secondary mb-0">{{ breadcrumb }}</p>
            </div>
          </div>
          <div v-if="title" class="row">
            <div class="d-flex align-items-center justify-content-between">
              <h4 class="mb-0 font-size-18 pb-4 fw-normal">{{ title }}</h4>
              <div class="page-title-right">
                <slot name="action"></slot>
              </div>
            </div>
          </div>
          <slot name="sub-title"></slot>
        </div>
        <div>
          <slot name="right-content"></slot>
        </div>
      </div>
      <div class="row">
        <slot></slot>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Page",
  props: {
    title: {
      type: String,
      required: true
    },
    breadcrumb: {
      type: String,
      default: null
    },
    showWarning: {
      type: Boolean,
      default: false
    },
    canGovernCase: {
      type: Boolean,
      default: false
    }
  }
}
</script>

<style lang="scss">
$warning-color: "--bs-danger";
$warning-height: 32px;
$warning-spacer: 28px;
$warning-border-width: 6px;

.page-content--warning {
  position: relative;

  &::before {
    content: "";
    position: absolute;
    top: 71px;
    left: 6px;
    right: 6px;
    bottom: 66px;
    min-height: calc(100vh - 70px - 66px - $warning-border-width);
    box-shadow: 0 0 0 $warning-border-width var(#{$warning-color});
  }
}

.page-content__container {
  .page-content--warning & {
    margin-top: -24px;
    padding-top: $warning-height + $warning-spacer;
  }
}

.page-content__warning-wrapper {
  .page-content--warning & {
    position: absolute;
    top: 66px;
    left: 0;
    right: 0;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0.5rem 0.5rem;
    background-color: var(#{$warning-color});
    color: var(--bs-white);
  }
}
</style>
