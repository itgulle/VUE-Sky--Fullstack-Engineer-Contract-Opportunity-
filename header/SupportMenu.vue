<template>
  <div class="dropdown d-none d-lg-inline-block ms-1">
    <button
      type="button"
      class="btn header-item noti-icon waves-effect"
      data-bs-toggle="dropdown"
      aria-haspopup="true"
      aria-expanded="false"
    >
      <i class="bx bx-aperture"></i>
    </button>
    <div class="dropdown-menu dropdown-menu-end">
      <a href="javascript:void(0);" class="dropdown-item">
        <span class="p-2"><i class="mdi mdi-help-box"></i> FAQ</span>
      </a>
      <router-link :to="{ name: 'tutorials' }" class="dropdown-item">
        <span class="p-2"><i class="mdi mdi-play-box"></i> Tutorials</span>
      </router-link>
      <a href="javascript:void(0);" class="dropdown-item">
        <span class="p-2"><i class="mdi mdi-comment-text-outline"></i> Contact us</span>
      </a>
      <a href="javascript:void(0);" class="dropdown-item">
        <span class="p-2"><i class="mdi mdi-bug"></i> Report a bug</span>
      </a>
      <a href="javascript:void(0);" class="dropdown-item">
        <span class="p-2"><i class="mdi mdi-lightbulb-on-outline"></i> Improve us</span>
      </a>
      <span class="p-2 dropdown-item">
        <img
          src="@/global/assets/images/MortgageBrainPadded.png"
          height="164"
          width="500"
          class="mx-auto d-block img-fluid"
          alt="Mortgage Brain"
        />
      </span>
    </div>
  </div>
</template>

<script>
export default {
  name: "SupportMenu"
}
</script>
