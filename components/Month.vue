<template>
  <div class="month grid grid-cols-7 h-full" :class="border">
    <div class="col-span-7 text-center" :class="border">
      {{ month }} {{ year }}
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
      v-for="(date, i) in days"
      :key="i"
      class="p-2 relative"
      :class="[
        border,
        text(i),
        forceFive && days.length > 35
          ? i > 27
            ? 'row-span-2'
            : 'row-span-4'
          : 'row-span-4',
        {
          end: i % 7 === 6,
          'border-b-0 pb-0': forceFive && i > 27 && i < 35,
          'border-t-0 pt-0': forceFive && i > 34,
        },
      ]"
    >
      <span v-if="date !== ''" :class="{'absolute bottom-2': forceFive && i > 34}">{{ date }}</span>
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
    forceFive: {
      type: Boolean,
      default: false,
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
    year() {
      return this.date.getFullYear();
    },
    month() {
      return new Intl.DateTimeFormat("default", options).format(this.date);
    },
    days() {
      let d = new Date(this.base);
      const month: number = new Date(this.base).getMonth();
      let slots: any[] = [];
      for (let i = this.base.getDay(); i > 0; i--) {
        if (this.allDates) {
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
      while (q > -1 && d.getDay() > q) {
        if (this.allDates && !this.forceFive) {
          slots.push(d.getDate());
        } else {
          slots.push("");
        }
        d.setDate(d.getDate() + 1);
      }
      return slots;
    },
  },
  methods: {
    text(i) {
      return this.forceFive && i > 34 ? 'text-left': 'text-right'
    }
  }
};
</script>
