<template>
  <NuxtLayout>
    <!-- Color mode: {{ $colorMode.value }} -->
    <select name="months" v-model="selectedMonth" class="bg-transparent">
      <option v-for="(month, i) in months" :key="i" :value="i">
        {{ month }}
      </option>
    </select>
    <input
      type="number"
      min="1900"
      v-model="selectedYear"
      class="bg-transparent"
    />
    <input
      type="checkbox"
      name="allDates"
      id="allDates"
      v-model="allDates"
    /><label for="allDates">View all dates</label>
    <input
      type="checkbox"
      name="hideYear"
      id="hideYear"
      v-model="hideYear"
    /><label for="hideYear">Hide year</label>
    <input
      type="checkbox"
      name="abbrDay"
      id="abbrDay"
      v-model="abbrDay"
    /><label for="abbrDayr">Abbreviate day</label>
    <Month :date="date" :allDates="allDates" :hideYear="hideYear" :abbrDay="abbrDay"></Month>
  </NuxtLayout>
</template>

<script setup>
import { ref, computed } from "vue";
const months = [
  "January",
  "February",
  "March",
  "April",
  "May",
  "June",
  "July",
  "August",
  "September",
  "October",
  "November",
  "December",
];
const selectedMonth = ref(new Date().getMonth());
const selectedYear = ref(new Date().getFullYear());
const allDates = ref(false);
const hideYear = ref(false);
const abbrDay = ref(false);
// a computed ref
const date = computed(() => {
  let d = new Date();
  d.setDate(1);
  d.setMonth(selectedMonth.value);
  d.setFullYear(selectedYear.value);
  return d;
});
</script>

<style>
html,
body,
#__nuxt {
  height: 100%;
}
body {
  background-color: #fff;
  color: rgba(0, 0, 0, 0.8);
}
.dark-mode body {
  background-color: #222;
  color: #fff;
}
</style>
