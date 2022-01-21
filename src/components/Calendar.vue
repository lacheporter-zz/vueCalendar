


<template id ="calendar">
  <div class="calendar">
    <div class="header">
      <img class="logo" src="../assets/logo.png" />
      <a class="arrow" @click="previousYear">&laquo;</a>
      <a class="arrow" @click="previousMonth">&lsaquo;</a>
      <span class="title" @click="selectedMonth">
        {{ header.label }}
      </span>
      <a class="arrow" @click="nextMonth">&rsaquo;</a>
      <a class="arrow" @click="nextYear">&raquo;</a>
    </div>
    <div class="weekdays">
      <div class="weekday" v-for="weekday in weekdays" :key="weekday.id">
        {{ weekday.label_3 }}
      </div>
    </div>
    <div class="week" v-for="week in weeks" :key="week.id">
      <div
        class="day"
        :class="{ today: day.isToday, 'not-in-month': !day.inMonth }"
        v-for="day in week"
        :key="day.id"
      >
        {{ day[dayKey] }}
      </div>
    </div>
  </div>
</template>


<script>
//import data for the calendar
const daysInMonths = [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31];
const weekdayList = [
  "Sunday",
  "Monday",
  "Tuesday",
  "Wednesday",
  "Thursday",
  "Friday",
  "Saturday",
];
const monthList = [
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
const current = new Date();
const currentDate = {
  year: current.getFullYear(),
  month: current.getMonth() + 1,
  day: current.getDate(),
};

export default {
  template: "#calendar",

  data() {
    return {
      monthList: [
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
      ],
      weekdayList: [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ],
      day: currentDate.day,
      month: currentDate.month,
      year: currentDate.year,
    };
  },

  props: {
    dayKey: { type: String, default: "label" },
  },

  computed: {
    monthIndex() {
      return this.month - 1;
    },
    isLeapYear() {
      return (
        (this.year % 4 === 0 && this.year % 100 !== 0) || this.year % 400 === 0
      );
    },

    previousMonthComps() {
      if (this.month === 1)
        return {
          days: daysInMonths[11],
          month: 12,
          year: this.year - 1,
        };
      return {
        days:
          this.month === 3 && this.isLeapYear
            ? 29
            : daysInMonths[this.month - 2],
        month: this.month - 1,
        year: this.year,
      };
    },

    header() {
      const month = this.monthList[this.monthIndex];
      return {
        month: month,
        year: this.year,
        label: month + " " + this.year,
      };
    },

    months() {
      return monthList.map((ml, i) => ({
        label: ml,
        label_1: ml.substring(0, 1),
        label_2: ml.substring(0, 2),
        label_3: ml.substring(0, 3),
        number: i + 1,
      }));
    },

    weekdays() {
      return weekdayList.map((wl, i) => ({
        label: wl,
        label_1: wl.substring(0, 1),
        label_2: wl.substring(0, 2),
        label_3: wl.substring(0, 3),
        number: i + 1,
      }));
    },

    weeks() {
      const weeks = [];
      let previousMonth = true;
      let thisMonth = false;
      let nextMonth = false;
      let day = this.previousMonthComps.days - this.firstWeekdayInMonth + 2;
      let month = this.previousMonthComps.month;
      let year = this.previousMonthComps.year;
      // Cycle through each week of the month, up to 6 total
      for (let w = 1; w <= 6 && !nextMonth; w++) {
        // Cycle through each weekday
        const week = [];
        for (let d = 1; d <= 7; d++) {
          // We need to know when to start counting actual month days
          if (previousMonth && d >= this.firstWeekdayInMonth) {
            // Reset day/month/year counters
            day = 1;
            month = this.month;
            year = this.year;
            // ...and flag we're tracking actual month days
            previousMonth = false;
            thisMonth = true;
          }

          // Append day info for the current week
          week.push({
            label: day && thisMonth ? day.toString() : "",
            day,
            weekday: d,
            week: w,
            month,
            year,
            date: new Date(year, month - 1, day),
            beforeMonth: previousMonth,
            afterMonth: nextMonth,
            inMonth: thisMonth,
            isToday:
              day === currentDate.day &&
              month === currentDate.month &&
              year === currentDate.year,
            isFirstDay: thisMonth && day === 1,
            isLastDay: thisMonth && day === this.daysInMonth,
          });

          // We've hit the last day of the month
          if (thisMonth && day >= this.daysInMonth) {
            thisMonth = false;
            nextMonth = true;
            day = 1;
            month = this.nextMonth.month;
            year = this.nextMonth.year;
            // Still in the middle of the month (hasn't ended yet)
          } else {
            day++;
          }
        }
        // Append week info for the month
        weeks.push(week);
      }
      return weeks;
    },

    firstWeekdayInMonth() {
      return new Date(this.year, this.monthIndex, 1).getDay() + 1;
    },

    daysInMonth() {
      return daysInMonths[this.monthIndex];
    },
  },

  methods: {
    selectedMonth() {
      this.month = currentDate.month;
      this.year = currentDate.year;
    },

    //continue here//
    nextMonth() {
      const { month, year } = this.nextMonth;
      this.month = month;
      this.year = year;
    },
    previousMonth() {
      const { month, year } = this.previousMonth;
      this.month = month;
      this.year = year;
    },
    nextYear() {
      this.year++;
    },
    previousYear() {
      this.year--;
    },
  },
};
</script>



<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.header {
  padding: 1em;
  display: flex;
  justify-content: center;
  align-items: center;
  color: white;
  background: #34495e;
}
.weekdays {
  display: flex;
  justify-content: space-evenly;
  background: #42b983;
  padding: 1rem;
}
.weekday {
  color: whitesmoke;
}
.title {
  font-size: 18px;
  color: white;
  margin-right: 10px;
}
.arrow {
  font-size: 2em;
  margin: 5px;
  cursor: pointer;
}
.logo {
  height: 50px;
  margin-right: 50px;
}
.week {
  display: flex;
}
.day {
  height: 100px;
  width: 100px;
  display: flex;
  justify-content: center;
  align-items: center;

  border: 1px solid rgb(143, 143, 143);
}
</style>