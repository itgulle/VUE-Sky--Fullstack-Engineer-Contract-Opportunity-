<template>
  <div id="mpa_div" class="inner mx-2">
    <h6>Mortgage Payment Calculator</h6>
    <form @submit.prevent>
      <div class="d-flex gap-3 mb-3">
        <div class="form-input-group">
          <label for="capitalinterest" class="col-form-label">
            Capital &amp; Interest
          </label>
          <input
            id="mpcal_capitalinterest"
            v-model.number="cal.capitalinterest"
            class="form-control"
            type="number"
            min="0"
            step="10"
            name="capitalinterest"
          />
        </div>
        <div class="form-input-group">
          <label
            id="mpcal_interestonlyamount"
            for="interestonlyamount"
            class="col-form-label"
            type="number"
            min="0"
            step="10"
          >
            Interest only amount
          </label>
          <input
            id="mpcal_interestonlyamount"
            v-model.number="cal.interestonlyamount"
            class="form-control"
            name="interestonlyamount"
            type="number"
            min="0"
            step="10"
          />
        </div>
      </div>
      <div class="row mb-3">
        <div class="form-input-group col-6">
          <label for="interestrate" class="col-form-label">Interest rate (%)</label>
          <input
            id="mpcal_interestrate"
            v-model.number="cal.interestrate"
            type="number"
            class="form-control"
            step="0.01"
            min="0"
            name="interestrate"
          />
        </div>

        <div class="form-input-group col-3">
          <label for="years" class="col-form-label">Years</label>
          <select id="mpcal_years" v-model="cal.years" class="form-select" name="years">
            <option v-for="year in options.years" :key="year" :value="year">
              {{ year }}
            </option>
          </select>
        </div>
        <div class="form-input-group col-3">
          <label for="months" class="col-form-label">Months</label>
          <select
            id="mpcal_months"
            v-model="cal.months"
            class="form-select"
            name="months"
          >
            <option v-for="month in options.months" :key="month" :value="month">
              {{ month }}
            </option>
          </select>
        </div>
      </div>
      <div class="row mt-3">
        <div class="col-7">
          <a class="btn btn-primary btn-lg mt-1" href="#" @click.stop="calculate">
            Calculate
          </a>
          <a href="javascript:void(0);" class="m-1 align-bottom fs-7" @click.stop="clear">
            Clear
          </a>
        </div>
        <div class="col-5">
          <span class="d-block">Monthly Payment</span>
          <span class="fs-4 fw-bold">{{ result }}</span>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
import { functionService } from "@/global/modules/helpers"
export default {
  name: "MortgagePaymentCalculator",
  data() {
    return {
      cal: {
        capitalInterest: 150000,
        interestOnlyAmount: 0,
        interestRate: 1.19,
        years: 25,
        months: 0
      },
      options: {
        years: Array.from({ length: 40 }, (_, i) => i + 1),
        months: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
      },
      result: ""
    }
  },
  methods: {
    calculate: function () {
      let ra = parseInt(this.cal.capitalinterest)
      let monthPart = parseInt(this.cal.months)
      let term = parseInt(this.cal.years) * 12 + monthPart
      let interestRate = parseFloat(this.cal.interestrate) / 1200

      let interestRateIO = parseFloat(this.cal.interestrate) / 100

      if (!this.cal.interestonlyamount) {
        this.cal.interestonlyamount = 0
      }

      let interestOnlyAmount = parseFloat(this.cal.interestonlyamount)
      let interestRatePercent = (interestOnlyAmount * interestRateIO) / 12

      let p =
        interestRatePercent +
        (ra * (interestRate * Math.pow(1 + interestRate, term))) /
          (Math.pow(1 + interestRate, term) - 1)

      if (!isNaN(p)) {
        this.result = functionService.formatNumberAsCurrency(p, { showPence: true })
      }

      console.log(this.result)
      return
    },

    clear: function () {
      this.cal.capitalinterest = ""
      this.cal.interestrate = ""
      this.cal.interestonlyamount = ""
      this.cal.result = ""
      this.cal.years = 25
      this.cal.months = 0
      this.result = ""
    }
  }
}
</script>
