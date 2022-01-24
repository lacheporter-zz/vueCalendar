<template id="event">
  <div class="eventForm">
    <h1>Event Info</h1>
    <p>Start: {{ startDate }}</p>
    <p>End: {{ endDate }}</p>

    <br />
    <p>Start date: {{ message }}</p>
    <input v-model="startDate" type="date" />
    <br />
    <p>End date {{ message }}</p>
    <input v-model="endDate" type="date" />
    <button v-on:click="sendDates">Submit</button>
  </div>
</template>

<script>
export default {
  name: "event",
  data() {
    return {
      startDate: "",
      endDate: "",
    };
  },
  methods: {
    sendDates() {
      this.$emit("datesChanged", this.startDate, this.endDate);
    },
  },
};
</script>


<style>
.eventForm {
  color: #2c3e50;
  width: 25%;
  border: 1px solid rgb(128, 128, 128, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}
input {
  padding: 0.5em;
}
button {
  margin-top: 50px;
  border: none;
  padding: 1em 2em;
  border-radius: 0.25em;
  background: #2c3e50;
  color: white;
}
button:hover {
  background: #35495e;
  cursor: pointer;
}
</style>