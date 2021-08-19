<template>
  <section class="forecast">
    <header class="forecast--title">
      <h3>8 day forecast</h3>
    </header>
    <ul>
      <li v-for="data in dailyData" :key="data.dt">
        {{ dateTime(data.dt) }}, {{ data.temp.day }} °C,
        {{ data.weather[0].description }}
      </li>
    </ul>
  </section>
</template>
<style scoped lang="scss">
.forecast {
  border-radius: 16px;
  padding: 16px;
  margin: 16px 0;
  background-color: #007d5f;
  color: #ffffff;

  &--title {
    h3 {
      font-size: 20px;
      margin: 18px 0;
    }
  }

  ul {
    text-align: left;
    font-size: 16px;

    li {
      padding: 0.5px 0;
    }
  }
}
</style>
<script>
export default {
  props: {
    dailyData: {
      type: [Object, Array],
      required: true,
    },
    timezone: {
      type: [String, Number],
      required: true,
    },
    timezoneOffset: {
      type: [String, Number],
      required: true,
    },
  },
  methods: {
    dateTime: function (givenTime) {
      const date = new Date(
        givenTime * 1000 + this.timezone * 1000 + this.timezoneOffset
      ).toLocaleDateString("en-GB", {
        day: "numeric",
        month: "short",
      });

      return date;
    },
  },
};
</script>
