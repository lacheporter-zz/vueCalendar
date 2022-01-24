


<template id ="calendar">
  <div class="calendar">
    <div class="weekdayHeader">
      <img class="logo" src="../assets/logo.png" />
      <a class="arrow" @click="previousYear">&laquo;</a>
      <a class="arrow" @click="prevMonth">&lsaquo;</a>
      <span class="title" @click="selectedMonth">
        {{ weekdayHeader.label }}
      </span>
      <a class="arrow" @click="nextMonth">&rsaquo;</a>
      <a class="arrow" @click="nextYear">&raquo;</a>
    </div>
    <div class="weekdays">
      <div class="weekday" v-for="weekday in weekdays" :key="weekday.id">
        {{ weekday.label }}
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
//calendar data
const current = new Date();
const currentDate = {
  year: current.getFullYear(),
  month: current.getMonth() + 1,
  day: current.getDate(),
};

export default {
  name: "calendar",
  data() {
    return {
      daysInMonths: [31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31],
      monthNames: [
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
      weekdayNames: [
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
    previousMonths() {
      //if this month is the first month
      if (this.month === 1)
        //return the days, months, and the year of the calendar
        return {
          days: this.daysInMonths[11],
          month: 12,
          year: this.year - 1,
        };
      return {
        days:
          this.month === 3 && this.isLeapYear
            ? 29
            : this.daysInMonths[this.month - 2],
        month: this.month - 1,
        year: this.year,
      };
    },
    weekdayHeader() {
      const month = this.monthNames[this.monthIndex];
      return {
        month,
        year: this.year,
        label: month + " " + this.day + "," + this.year,
      };
    },
    months() {
      //map through month names and pass to template
      return this.monthNames.map((ml) => ({
        label: ml,
      }));
    },
    weekdays() {
      //map through week names and pass to template
      return this.weekdayNames.map((wl) => ({
        label: wl,
      }));
    },
    weeks() {
      //create array of for weeks
      const weeks = [];
      //initial values to keep track of start, end, day of month
      let monthStarted = false;
      let monthEnded = false;
      let monthDay = 0;
      // Cycle through weeks in month, should be 6 for a calendar view
      for (let w = 1; w <= 6 && !monthEnded; w++) {
        // Create array for days in a week
        const week = [];
        // Cycle through days in a week, should be 7
        for (let d = 1; d <= 7; d++) {
          // We need to know when to start counting actual month days
          if (!monthStarted && d >= this.firstWeekdayInMonth) {
            // Start the day counter
            monthDay = 1;
            // Start tracking month days
            monthStarted = true;
          } else if (monthStarted && !monthEnded) {
            // Increment the day counter
            monthDay += 1;
          }

          // Append day info into week array
          week.push({
            label: monthDay ? monthDay.toString() : "",
            number: monthDay,
            weekdayNumber: d,
            weekNumber: w,
            beforeMonth: !monthStarted,
            afterMonth: monthEnded,
            inMonth: monthStarted && !monthEnded,
            isToday:
              monthDay === currentDate.day &&
              this.month === currentDate.month &&
              this.year === currentDate.year,
            isFirstDay: monthDay === 1,
            isLastDay: monthDay === this.daysInMonth,
          });

          // Trigger end of month on the last day
          if (monthStarted && !monthEnded && monthDay >= this.daysInMonth) {
            monthDay = 0;
            monthEnded = true;
          }
        }
        // Append week info for the month
        weeks.push(week);
      }
      //Returns pushed weeks
      return weeks;
    },

    firstWeekdayInMonth() {
      return new Date(this.year, this.monthIndex, 1).getDay() + 1;
    },

    daysInMonth() {
      //check if febuary is leap year
      const isFebruary = this.month === 2;
      const isLeapYear =
        (this.year % 4 == 0 && this.year % 100 != 0) || this.year % 400 == 0;
      if (isFebruary && isLeapYear) return 29;
      return this.daysInMonths[this.monthIndex];
    },
  },

  methods: {
    selectedMonth() {
      this.month = currentDate.month;
      this.year = currentDate.year;
    },
    nextMonth() {
      if (this.month < 12) {
        this.month++;
      } else {
        this.month = 1;
        this.year++;
      }
    },
    prevMonth() {
      if (this.month > 1) {
        this.month--;
      } else {
        this.month = 12;
        this.year--;
      }
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
.calendar {
  display: flex;
  flex-direction: column;
  width: 75%;
  height: 100%;
}
.weekdayHeader {
  padding: 0.5em;
  display: flex;
  justify-content: center;
  align-items: center;
  color: white;
  background: #34495e;
}
.weekdays {
  display: flex;
  justify-content: space-between;
  background: #42b983;
  padding: 1rem;
}
.weekday {
  color: whitesmoke;
}
.title {
  font-size: 24px;
  color: white;
  margin-right: 10px;
  margin-left: 10px;
  font-weight: 500;
}
.arrow {
  font-size: 3em;
  margin: 5px;
  cursor: pointer;
}
.logo {
  height: 50px;
  margin-right: 50px;
}
.week {
  display: flex;
  height: 100%;
}
.day {
  height: 100%;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  border: 1px solid rgb(143, 143, 143);
}
.day:hover {
  cursor: pointer;
  background: #42b98325;
  transition: 0.1s;
}
</style>