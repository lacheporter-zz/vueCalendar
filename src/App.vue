

<template>
  <div id="app">
    <Event @datesChanged="updateEvent" />
    <Calendar :start="start" :end="end" />
  </div>
</template>



<script>
import Event from "./components/Event.vue";
import Calendar from "./components/Calendar.vue";
export default {
  name: "App",
  data() {
    return { start: null, end: null };
  },

  components: {
    Event,
    Calendar,
  },
  methods: {
    updateDates(start, end) {
      this.start = start;
      this.end = end;
    },
  },
};
</script>


<style>
* {
  padding: 0;
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  margin: 10px;
  height: 95vh;
}
</style>
