<template>
  <div class="month grid grid-cols-7 h-full" :class="border">
    <!-- <pre class="h-[200px] overflow-auto col-span-7">{{ dates }}</pre> -->
    <div
      class="col-span-7 text-center flex gap-[0.5ch] items-center px-4 text-2xl"
      :class="[border, displayMonth]"
    >
      <span class="hidden print:flex print:gap-[0.5ch]"
        ><span class="font-bold">{{ month }}</span> {{ year }}</span
      >
      <slot></slot>
    </div>
    <Weekday :abbrDay="abbrDay" :startMonday="startMonday"></Weekday>
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
      <template
        v-if="
          date.current ||
          (allDates && !forceFive) ||
          (allDates && forceFive && i < 35)
        "
      >
        <span
          :class="[
            'absolute m-2 text-right leading-none',
            {
              'top-0 right-0': !forceFive || (forceFive && i < 35),
              'bottom-0 !text-left': forceFive && i > 34,
              'opacity-50': !date.current,
            },
          ]"
          ><span :class="today(date)">{{ date.date }}</span
          ><span
            v-if="holiday(date).length > 0"
            class="text-xs leading-none print:text-[8pt]"
            ><template v-for="name in holiday(date)"
              ><br />{{ name }}</template
            ></span
          >
        </span>
      </template>
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
import hldys from "~/src/holidays.json";
const holidays = hldys.holidays;
const today: Date = new Date();
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
    startMonday: {
      type: Boolean,
      default: false,
    },
  },
  data() {
    return {
      border: "border border-current",
      holidays: holidays,
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
      // Get days to fill week
      const ddd = this.startMonday
        ? this.base.getDay() === 0
          ? 6
          : this.base.getDay() - 1
        : this.base.getDay();
      for (let i = ddd; i > 0; i--) {
        let s = new Date(d);
        s.setDate(s.getDate() - i);
        slots.push({ obj: s, date: s.getDate(), current: false });
      }
      while (d.getMonth() === month) {
        let s = new Date(d);
        slots.push({ obj: s, date: s.getDate(), current: true });
        d.setDate(d.getDate() + 1);
      }
      const _dGetDay = this.startMonday ? d.getDay() === 0 ? 6 : d.getDay() - 1 : d.getDay();
      const q = _dGetDay - 1;
      // no, I can't reference _dGetDay here because it will crash
      while (q > -1 && (this.startMonday ? d.getDay() === 0 ? 6 : d.getDay() - 1 : d.getDay()) > q) {
        let s = new Date(d);
        slots.push({ obj: s, date: s.getDate(), current: false });
        d.setDate(d.getDate() + 1);
      }
      return slots;
    },
    month() {
      const options: object = { month: "long" };
      let d: any = this.date;
      return new Intl.DateTimeFormat("default", options).format(d);
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
        ? "bg-indigo-600 text-white dark:bg-indigo-400 dark:text-black aspect-square h-6 inline-grid place-items-center rounded-full print:text-current"
        : "";
    },
    holiday(date: any) {
      let h: string[] = [];
      holidays.forEach((e) => {
        if (e.static && e.date) {
          let _date = e.date;
          if (_date === date.date && e.month === date.obj.getMonth()) {
            h.push(e.name);
          }
          if (e.observed) {
            let holiday = new Date(this.year, e.month, _date);
            let day = holiday.getDay();
            let weekend = day % 6 === 0;
            if (weekend) {
              let offset = day === 0 ? 1 : day === 6 ? -1 : 0;
              _date = _date + offset;
              let _hldy = new Date(this.year, e.month, _date);
              if (_hldy.toString() === date.obj.toString()) {
                h.push(e.name + " (Observed)");
              }
            }
          }
          // trying to observe new years if it's on saturday, maybe I'll figure out a better way one day (but today ain't it)
          if (
            e.month === 11 &&
            date.obj.getMonth() === 11 &&
            date.date === 31 &&
            e.date === 31 &&
            date.obj.getDay() === 5
          ) {
            h.push("New Year’s Day (Observed)");
          }
        } else if (
          e.month === date.obj.getMonth() &&
          // e.day === date.obj.getDay() &&
          e.instance !== null
        ) {
          let ex_day = e.exceptions?.add_day ? e.exceptions?.add_day : 0;
          let findDate = this.dates.filter(
            (f) =>
              f.obj.getDay() === e.day &&
              f.obj.getMonth() === this.date.getMonth()
          );
          let isDate =
            findDate[
              e.instance < 0 ? findDate.length + e.instance : e.instance - 1
            ];
          let _dex = isDate.obj.getDate() + ex_day;
          if (date.date === _dex) {
            h.push(e.name);
          }
        }
      });
      return h;
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
