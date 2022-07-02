<template>
  <NuxtLayout>
    <!-- Color mode: {{ $colorMode.value }} -->
    <div class="print:hidden flex items-center">
      <i class="fa-solid fa-gear"></i>
      <input
        type="checkbox"
        name="allDates"
        id="allDates"
        v-model="allDates"
      /><label for="allDates">View all dates</label>
      <input
        type="checkbox"
        name="abbrDay"
        id="abbrDay"
        v-model="abbrDay"
      /><label for="abbrDay">Abbreviate day</label>
      <input
        type="checkbox"
        name="forceFive"
        id="forceFive"
        v-model="forceFive"
      /><label for="forceFive">Force five week view</label>
      <select name="monthPos" v-model="monthPos" class="bg-transparent">
        <option v-for="(op, i) in monthPosOptions" :key="i" :value="op">
          {{ op }}
        </option>
      </select>
      <div class="ml-auto flex gap-2">
        <Button @click="prevMonth()">prev</Button>
        <Button @click="goToToday()">today</Button>
        <Button @click="nextMonth()">next</Button>
      </div>
    </div>
    <Month
      :date="date"
      :allDates="allDates"
      :abbrDay="abbrDay"
      :forceFive="forceFive"
      :monthPos="monthPos"
    >
    <select name="months" v-model="selectedMonth" class="bg-transparent font-bold print:hidden w-fit">
        <option v-for="(month, i) in months" :key="i" :value="i">
          {{ month }}
        </option>
      </select>
      <input
        type="number"
        min="1900"
        v-model="selectedYear"
        class="bg-transparent print:hidden w-[5ch]"
      />
    </Month>
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
const monthPosOptions = ["left", "center", "right"];
const selectedMonth = ref(new Date().getMonth());
const selectedYear = ref(new Date().getFullYear());
const allDates = ref(false);
const abbrDay = ref(false);
const forceFive = ref(false);
const monthPos = ref("center");
const date = computed(() => {
  let d = new Date();
  d.setDate(1);
  d.setFullYear(selectedYear.value);
  d.setMonth(selectedMonth.value);
  return d;
});
function nextMonth() {
  selectedMonth.value++;
  selectedYear.value = date.value.getFullYear();
  selectedMonth.value = selectedMonth.value % 12;
}
function prevMonth() {
  selectedMonth.value--;
  selectedYear.value = date.value.getFullYear();
  if (selectedMonth.value < 0)
    selectedMonth.value = (selectedMonth.value + 12) % 12;
}
function goToToday() {
  selectedMonth.value = new Date().getMonth();
  selectedYear.value = new Date().getFullYear();
}
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
