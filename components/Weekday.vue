<template>
  <div
    v-for="(weekday, i) in weekdays"
    :key="i"
    class="grid place-items-center"
    :class="border"
  >
    <span
      :class="{ 'md:hidden print:hidden': !abbrDay, 'print:inline': abbrDay }"
      >{{ weekday.slice(0, 3) }}</span
    >
    <span
      class="hidden md:inline"
      :class="{ 'md:hidden print:hidden': abbrDay, 'print:inline': !abbrDay }"
      >{{ weekday }}</span
    >
  </div>
</template>

<script setup lang="ts">
const border: string = "border border-current";
const weekdays: string[] = [
  "Sunday",
  "Monday",
  "Tuesday",
  "Wednesday",
  "Thursday",
  "Friday",
  "Saturday",
];
defineProps<{
  abbrDay: boolean;
}>();
</script>
