<template>
  <div class="month grid grid-cols-7 h-full" :class="border">
    <div
      class="col-span-7 text-center flex gap-[0.5ch] items-center px-4 text-2xl"
      :class="[border, displayMonth]"
    >
      <span class="hidden print:flex print:gap-[0.5ch]"
        ><span class="font-bold">{{ month }}</span> {{ year }}</span
      >
      <slot></slot>
    </div>
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
    <div
      v-for="(date, i) in dates"
      :key="i"
      class="relative flex"
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
          'absolute m-2',
          today(date),
          {
            'top-0 right-0': !forceFive || (forceFive && i < 35),
            'bottom-0': forceFive && i > 34,
            'opacity-50': !date.current,
          },
        ]"
        >{{ date.date }}
      </span>
      <svg
        v-if="forceFive && i > 27 && i < 35 && dates[i + 7]?.current"
        width="1"
        height="1"
        class="absolute h-full w-full top-0 left-0 split-top"
        aria-hidden="true"
      >
        <rect width="100%" height="100%" fill="currentColor" />
      </svg>
      <svg
        v-if="forceFive && i > 34 && date.current"
        width="1"
        height="1"
        class="absolute h-full w-full top-0 left-0 split-bottom"
        aria-hidden="true"
      >
        <rect width="100%" height="100%" fill="currentColor" />
      </svg>
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
    text(i: number) {
      return this.forceFive && i > 34 ? "" : "items-start justify-end";
    },
    today(date: any) {
      return date.obj.toDateString() === today.toDateString()
        ? "bg-indigo-600 text-white dark:bg-indigo-400 dark:text-black aspect-square h-6 grid place-items-center rounded-full print:text-current"
        : "";
    },
  },
};
</script>

<style scoped>
.split-top {
  clip-path: polygon(
    0 1px,
    0 0,
    1px 0,
    calc(50% + 1px) 100%,
    50% 100%,
    calc(50% - 1px) 100%
  );
}
.split-bottom {
  clip-path: polygon(
    calc(50% - 1px) 0,
    calc(50% + 1px) 0,
    100% calc(100% - 1px),
    100% 100%,
    calc(100% - 1px) 100%
  );
}
</style>
