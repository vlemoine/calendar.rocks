<template>
  <div class="col-span-7 grid grid-cols-7">
    <div
      v-for="(weekday, i) in weekdays"
      :key="i"
      class="grid place-items-center"
      :class="[border, {'order-last': last(weekday)}]"
    >
      <span class="md:hidden print:hidden">{{ display(weekday, true) }}</span>
      <span class="hidden md:inline print:inline">{{ display(weekday) }}</span>
    </div>
  </div>
</template>

<script setup lang="ts">
const props = defineProps<{
  abbrDay: boolean;
  startMonday: boolean;
}>();
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
const last = (weekday: string) => {
  let wdi = weekdays.indexOf(weekday);
  wdi = props.startMonday ? (wdi === 0 ? 6 : wdi - 1) : wdi;
  return wdi === 6;
};
const display = (weekday: string, force: boolean = false) => {
  const abbr = weekday.slice(0, 3);
  return force ? abbr : props.abbrDay ? abbr : weekday;
};
</script>
