<template>
  <div class="month grid grid-cols-7 h-full" :class="border">
    <div
      class="col-span-7 text-center flex gap-[0.5ch] items-center px-4 text-2xl"
      :class="[border, displayMonth]"
    >
      <span class="hidden print:flex print:gap-[0.5ch]"><span class="font-bold">{{ month }}</span> {{ year }}</span>
      <slot></slot>
    </div>
    <div
      v-for="(weekday, i) in weekdays"
      :key="i"
      class="text-center"
      :class="border"
    >
      {{ abbrDay ? weekday.slice(0, 3) : weekday }}
    </div>
    <div
      v-for="(date, i) in dates"
      :key="i"
      class="px-2 py-1 relative flex"
      :class="[
        border,
        text(i),
        forceFive && dates.length > 35
          ? i > 27
            ? 'row-span-3'
            : 'row-span-6'
          : 'row-span-6',
        {
          end: i % 7 === 6,
          'border-b-0 pb-0': forceFive && i > 27 && i < 35,
          'border-t-0 pt-0': forceFive && i > 34,
        },
      ]"
    >
      <span
        v-if="
          date.current ||
          (allDates && !forceFive) ||
          (allDates && forceFive && i < 35)
        "
        :class="[
          today(date),
          {
            'absolute bottom-2': forceFive && i > 34,
            'opacity-50': !date.current,
          },
        ]"
        >{{ date.date }}
      </span>
    </div>
  </div>
</template>

<script lang="ts">
const today: Date = new Date();
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
    date: {
      type: Object,
      required: true,
    },
    allDates: {
      type: Boolean,
      default: false,
    },
    abbrDay: {
      type: Boolean,
      default: false,
    },
    // TODO: week view: 5, 6, auto
    forceFive: {
      type: Boolean,
      default: false,
    },
    monthPos: {
      type: String,
      default: "center",
    },
  },
  data() {
    return {
      border: "border border-current",
      weekdays: weekdays,
    };
  },
  computed: {
    base() {
      return new Date(this.date.getFullYear(), this.date.getMonth());
    },
    dates() {
      let d = new Date(this.base);
      const month: number = new Date(this.base).getMonth();
      let slots: any[] = [];
      for (let i = this.base.getDay(); i > 0; i--) {
        let s = new Date(d);
        s.setDate(s.getDate() - i);
        slots.push({ obj: s, date: s.getDate(), current: false });
      }
      while (d.getMonth() === month) {
        let s = new Date(d);
        slots.push({ obj: s, date: s.getDate(), current: true });
        d.setDate(d.getDate() + 1);
      }
      let q = d.getDay() - 1;
      while (q > -1 && d.getDay() > q) {
        let s = new Date(d);
        slots.push({ obj: s, date: s.getDate(), current: false });
        d.setDate(d.getDate() + 1);
      }
      return slots;
    },
    month() {
      return new Intl.DateTimeFormat("default", options).format(this.date);
    },
    year() {
      return this.date.getFullYear();
    },
    displayMonth() {
      return this.monthPos === "center"
        ? "justify-center"
        : this.monthPos === "right"
        ? "justify-end"
        : "";
    },
  },
  methods: {
    text(i) {
      return this.forceFive && i > 34 ? "" : "items-start justify-end";
    },
    today(date) {
      return date.obj.toDateString() === today.toDateString()
        ? "bg-indigo-600 text-white dark:bg-indigo-400 dark:text-black aspect-square h-6 grid place-items-center -mr-1 rounded-full print:text-current"
        : "";
    },
  },
};
</script>
