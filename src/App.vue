<template>
  <div class="container h-100 d-flex justify-content-center align-items-center">
    <h1 v-if="!currencyRate" class="text-white text-center mb-5">
      Something went wrong! Please refresh this page.
    </h1>
    <div v-if="currencyRate">
      <h1 class="text-white text-center mb-3">Currency converter</h1>
      <p class="text-light text-center">
        Currency rate: 1 {{ firstCurrency }} = {{ currencyRate.toFixed(5) }}
        {{ secondCurrency }}.
      </p>
      <div
        class="d-sm-flex text-center justify-content-center align-items-center"
      >
        <div class="input-group">
          <div class="input-group-prepend">
            <select
              class="form-control form-select-lg rounded-start rounded-0 bg-light text-truncate mw-200"
              v-model="firstCurrency"
              @change="getCurrencies()"
            >
              <option
                v-for="(el, key) in currencyNames"
                :key="key"
                :value="key"
              >
                {{ el }}
              </option>
            </select>
          </div>
          <input type="number" class="form-control" v-model="userAmount" />
        </div>
        <button class="btn" @click="swapSides()">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="32"
            height="32"
            fill="currentColor"
            class="bi bi-arrow-repeat text-white"
            viewBox="0 0 16 16"
          >
            <path
              d="M11.534 7h3.932a.25.25 0 0 1 .192.41l-1.966 2.36a.25.25 0 0 1-.384 0l-1.966-2.36a.25.25 0 0 1 .192-.41zm-11 2h3.932a.25.25 0 0 0 .192-.41L2.692 6.23a.25.25 0 0 0-.384 0L.342 8.59A.25.25 0 0 0 .534 9z"
            />
            <path
              fill-rule="evenodd"
              d="M8 3c-1.552 0-2.94.707-3.857 1.818a.5.5 0 1 1-.771-.636A6.002 6.002 0 0 1 13.917 7H12.9A5.002 5.002 0 0 0 8 3zM3.1 9a5.002 5.002 0 0 0 8.757 2.182.5.5 0 1 1 .771.636A6.002 6.002 0 0 1 2.083 9H3.1z"
            />
          </svg>
        </button>
        <div class="input-group">
          <div class="input-group-prepend">
            <select
              class="form-control form-select-lg rounded-start rounded-0 bg-light text-truncate mw-200"
              v-model="secondCurrency"
              @change="getCurrencies()"
            >
              <option
                v-for="(el, key) in currencyNames"
                :key="key"
                :value="key"
              >
                {{ el }}
              </option>
            </select>
          </div>
          <input
            type="number"
            class="form-control"
            readonly
            :value="convertedValue"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { currencyNames } from './helpers/currencies';
export default {
  name: 'App',
  components: {},
  data() {
    return {
      currencyNames,
      firstCurrency: 'USD',
      secondCurrency: 'EUR',
      currencyRate: null,
      userAmount: 1,
    };
  },
  methods: {
    async getCurrencies() {
      await fetch(
        `https://api.apilayer.com/exchangerates_data/convert?to=${this.firstCurrency}&from=${this.secondCurrency}&amount=1&apikey=KbvIQxFxfdGBkhSQxQMVakVO1zNDs8W4`
      )
        .then((response) => response.json())
        .then((json) => {
          this.currencyRate = json.result;
        });
    },
    swapSides() {
      [this.firstCurrency, this.secondCurrency] = [
        this.secondCurrency,
        this.firstCurrency,
      ];
      this.getCurrencies();
    },
  },
  async mounted() {
    await this.getCurrencies();
  },
  computed: {
    convertedValue() {
      if (this.userAmount % this.currencyRate === 0) {
        return this.userAmount / this.currencyRate;
      }
      return (this.userAmount / this.currencyRate).toFixed(2);
    },
  },
};
</script>

<style>
#app {
  height: 100vh;
  background: hsla(237, 100%, 50%, 1);

  background: linear-gradient(
    135deg,
    hsla(237, 100%, 50%, 1) 0%,
    hsla(186, 100%, 69%, 1) 100%
  );
}
.mw-200 {
  max-width: 200px;
}
</style>
