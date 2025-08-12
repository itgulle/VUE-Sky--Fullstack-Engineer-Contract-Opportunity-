<template>
  <footer class="footer">
    <div class="container-fluid">
      <div class="row">
        <div class="col-sm-6">{{ year }} &copy; Mortgage Brain Ltd.</div>
        <div class="col-sm-6">
          <div class="text-sm-end d-none d-sm-block">
            <a href="https://www.mortgagebrain.co.uk/privacy-policy" target="_blank"
              >Privacy</a
            >
          </div>
        </div>
      </div>
    </div>
  </footer>
</template>

<script>
import { format } from "date-fns"
import { footerCopyRightDate } from "@/global/modules/enum/constants"

export default {
  computed: {
    year() {
      return format(new Date(), footerCopyRightDate)
    }
  }
}
</script>

<style scoped>
.footer a {
  margin-left: 32px;
  cursor: pointer;
  color: #74788d;
}
</style>
