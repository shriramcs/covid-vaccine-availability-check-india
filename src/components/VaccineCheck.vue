<template>
  <div>
    <h2>Check for Vaccines availability in your district within India</h2>
    <div>eg : For Koppal district - 583231</div>
    <p>
      <label for="pincode"> PIN </label>
      <input v-model="pin" placeholder="583231" />
    </p>
    <p>
      <button @click="getAvailableSlots(18)" :disabled="loading">
        Check 18+
      </button>
      <button @click="getAvailableSlots(45)" :disabled="loading">
        Check 45+
      </button>
    </p>
    <section>
      <p>{{ loading ? "Loading" : "" }} {{ result }}</p>
    </section>
  </div>
</template>

<script>
import { getCurrentDate } from "../shared/util.js";
const COWIN_CLONE_PIN = "COWIN_CLONE_PIN";
export default {
  name: "VaccineCheck",
  data() {
    return {
      result: "",
      loading: false,
      pin: 583231,
      age: 18,
    };
  },
  methods: {
    getAvailableSlots(age) {
      if (this.isPinInvalid(this.pin) || this.loading) {
        return;
      }
      this.age = age;
      this.loading = true;
      this.result = "";
      localStorage.setItem(COWIN_CLONE_PIN, this.pin);
      fetch(
        `https://cdn-api.co-vin.in/api/v2/appointment/sessions/public/calendarByPin?pincode=${
          this.pin
        }&date=${getCurrentDate()}`
      )
        .then((d) => d.json())
        .then(this.handleSuccess)
        .catch(this.handleError);
    },
    handleSuccess(result) {
      this.result = ((result || {}).centers || []).some(
        (d) =>
          (d.sessions || []).filter((t) => t.min_age_limit === this.age).length
      )
        ? " Vaccines are available"
        : "Sorry! Vaccines are not available";
      this.loading = false;
    },
    handleError() {
      this.loading = false;
      this.result = "Error while checking availability";
    },
    isPinInvalid(pin) {
      return !(pin && (pin || "").toString().length === 6);
    },
  },
  created() {
    this.pin = localStorage.getItem(COWIN_CLONE_PIN) || this.pin;
  },
};
</script>
<style scoped>
button {
  margin-right: 1rem;
}
</style>
