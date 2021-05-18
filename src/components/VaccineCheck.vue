<template>
  <div>
    <h2>Check for Vaccines availability in your district within India</h2>
    <div>eg : For Koppal district - 583231</div>
    <p>
      <label for="pincode"> PIN </label>
      <input v-model="pin" placeholder="583231" />
    </p>
    <p>
      <button @click="getAvailableSlots(18)">Check 18+</button>
      <button @click="getAvailableSlots(45)">Check 45+</button>
    </p>
    <section>
      <p>{{ loading ? "Loading" : "" }} {{ result }}</p>
    </section>
  </div>
</template>

<script>
export default {
  name: "VaccineCheck",
  data() {
    return {
      result: "",
      loading: false,
      pin: 583231,
    };
  },
  methods: {
    async getAvailableSlots(age) {
      if (this.isPinInvalid(this.pin)) {
        return;
      }
      this.loading = true;
      this.result = "";
      // https://www.cowin.gov.in/home
      const result = await fetch(
        `https://cdn-api.co-vin.in/api/v2/appointment/sessions/public/calendarByPin?pincode=${
          this.pin
        }&date=${this.getCurrentDate()}`
      ).then(async (d) => await d.json());
      this.result = ((result || {}).centers || []).some(
        (d) => (d.sessions || []).filter((t) => t.min_age_limit === age).length
      )
        ? " Vaccines are available"
        : "Sorry! Vaccines are not available";
      this.loading = false;
    },
    isPinInvalid(pin) {
      return !(pin && (pin || "").toString().length === 6);
    },
    getCurrentDate() {
      let today = new Date();
      let dd = today.getDate();
      let mm = today.getMonth() + 1;
      let yyyy = today.getFullYear();
      if (dd < 10) {
        dd = "0" + dd;
      }
      if (mm < 10) {
        mm = "0" + mm;
      }
      return dd + "-" + mm + "-" + yyyy;
    },
  },
};
</script>
<style scoped>
button {
  margin-right: 1rem;
}
</style>
