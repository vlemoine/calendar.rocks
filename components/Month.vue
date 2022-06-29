<template>
  <div class="month grid grid-cols-7 h-full" :class="border">
    <div class="col-span-7 text-center" :class="border">{{ month }}</div>
    <div
      v-for="(weekday, i) in weekdays"
      :key="i"
      class="text-center" :class="border"
    >
      {{ weekday }}
    </div>
    <div
      v-for="(date, i) in days"
      :key="i"
      class="p-2 text-right row-span-4" :class="border"
    >
      {{ date }}
    </div>
  </div>
</template>

<script lang="ts">
const weekdays: string[] = [
  "Sunday",
  "Monday",
  "Tuesday",
  "Wednesday",
  "Thursday",
  "Friday",
  "Saturday",
];
const options: object = { month: "long" };

export default {
  props: {
    date: Object,
    allDays: {
      type: Boolean,
      default: true,
    },
  },
  data() {
    return {
      border: 'border border-black dark:border-white',
      weekdays: weekdays,
    };
  },
  computed: {
    base() {
      return new Date(this.date.getFullYear(), this.date.getMonth());
    },
    month() {
      return new Intl.DateTimeFormat("default", options).format(this.date);
    },
    days() {
      let d = new Date(this.base);
      const month: number = new Date(this.base).getMonth();
      let slots: any[] = [];
      for (let i = this.base.getDay(); i > 0; i--) {
        if (this.allDays) {
          let s = new Date(d);
          s.setDate(s.getDate() - i);
          slots.push(s.getDate());
        } else {
          slots.push("");
        }
      }
      while (d.getMonth() === month) {
        slots.push(d.getDate());
        d.setDate(d.getDate() + 1);
      }
      let q = d.getDay() - 1;
      while (d.getDay() > q) {
         if (this.allDays) {
          slots.push(d.getDate());
        } else {
          slots.push("");
        }
          d.setDate(d.getDate() + 1);
      }
      return slots;
    },
  },
};
</script>
