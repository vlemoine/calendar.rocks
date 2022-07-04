<template>
  <NuxtLayout>
    <Html lang="en"></Html>
    <Head><Title>calendar.rocks</Title></Head>
    <Body class="dark:bg-gray-900 text-black/80 dark:text-white"></Body>
    <h1 class="sr-only">{{ months[selectedMonth] }} {{ selectedYear }}</h1>
    <!-- Color mode: {{ $colorMode.value }} -->
    <div id="Toolbar" class="print:hidden flex items-center mb-2 -mt-2 gap-2">
      <div class="mr-auto flex gap-2">
        <Button
          @click="prevMonth()"
          aria-label="Previous Month"
          title="Previous Month"
          class="aspect-square h-8"
          ><font-awesome-icon icon="fa-solid fa-angle-left"
        /></Button>
        <Button @click="goToToday()"
          ><font-awesome-icon icon="fa-solid fa-calendar-day" /> Today</Button
        >
        <Button
          @click="nextMonth()"
          aria-label="Next Month"
          title="Next Month"
          class="aspect-square h-8"
          ><font-awesome-icon icon="fa-solid fa-angle-right"
        /></Button>
      </div>
      <Button
        @click="options = !options"
        aria-label="Calendar options"
        title="Calendar options"
        :class="{ '!bg-indigo-100 dark:!bg-indigo-900': options }"
        ><font-awesome-icon icon="fa-solid fa-gear"
      /></Button>
      <Button
        aria-label="Print"
        title="Print"
        @click="print()"
        class="!bg-lime-400 dark:!bg-lime-600 aspect-square h-16 rounded-full fixed right-3 bottom-3 z-50 shadow-lg !p-0 md:static md:h-12"
        ><font-awesome-icon icon="fa-solid fa-print"
      /></Button>
    </div>
    <div
      id="Options"
      class="print:hidden bg-indigo-500/20 p-4 rounded mb-2 flex gap-2 items-center"
      :class="{ hidden: !options }"
    >
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
      <select
        name="monthPos"
        v-model="monthPos"
        class="bg-transparent"
        aria-label="Month position"
      >
        <option v-for="(op, i) in monthPosOptions" :key="i" :value="op">
          {{ op }}
        </option>
      </select>
    </div>
    <Month
      :date="date"
      :allDates="allDates"
      :abbrDay="abbrDay"
      :forceFive="forceFive"
      :monthPos="monthPos"
    >
      <select
        name="months"
        v-model="selectedMonth"
        class="bg-transparent font-bold print:hidden border border-black/20 dark:border-white/20 rounded h-[1.5em]"
        aria-label="Month"
      >
        <option v-for="(month, i) in months" :key="i" :value="i">
          {{ month }}
        </option>
      </select>
      <input
        aria-label="Year"
        type="number"
        min="1900"
        v-model="selectedYear"
        class="bg-transparent print:hidden w-[6ch] border border-black/20 dark:border-white/20 rounded h-[1.5em] px-[.25ch]"
      />
    </Month>
    <div class="p-3 print:hidden"></div>
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
const options = ref(false);
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
function print() {
  window.print();
}
</script>

<style>
html,
body,
#__nuxt {
  height: 100%;
}
</style>
