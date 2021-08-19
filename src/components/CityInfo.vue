<template>
  <section class="city">
    <header class="city--title">
      <h3>{{ city }}, {{ country }}</h3>
      <aside class="city--title--date">{{ time }}</aside>
    </header>
    <article class="city--weather">
      <p class="city--weather--temperature">
        Average {{ temp }}°C, but feels like {{ sensedTemp }}°C
      </p>
      <div class="city--weather--extra">
        <ul>
          <li v-for="data in weatherDesc" :key="data.id">
            {{ data.description }}
          </li>
        </ul>
        <ul>
          <li>Wind: {{ windSpeed }} m/s, {{ windDirection }}</li>
          <li>Pressure: {{ pressure }} hPa</li>
          <li>Humidity: {{ humidity }} %</li>
          <li>Viibility: {{ visibility / 1000 }} km</li>
        </ul>
      </div>
    </article>
  </section>
</template>
<style scoped lang="scss">
.city {
  border-radius: 16px;
  padding: 16px;
  margin: 16px 0;
  background-color: #27876f;
  color: #ffffff;

  &--title {
    position: relative;
    h3 {
      font-size: 20px;
      margin: 18px 0;
    }

    &--date {
      position: absolute;
      left: 0;
      top: -18px;
      font-size: 14px;
    }
  }

  &--weather {
    font-size: 18px;

    &--extra {
      display: flex;
      justify-content: space-evenly;

      ul {
        width: 50%;
        text-align: left;
        font-size: 16px;
      }
    }
  }
}
</style>
<script>
export default {
  props: {
    city: {
      type: [String],
      required: true,
    },
    country: {
      type: [String],
      required: true,
    },
    time: {
      type: [String, Number],
      required: true,
    },
    temp: {
      type: [String, Number],
      required: true,
    },
    sensedTemp: {
      type: [String, Number],
      required: true,
    },
    weatherDesc: {
      type: [Array, Object],
      required: true,
    },
    windSpeed: {
      type: [String, Number],
      required: true,
    },
    windDirection: {
      type: [String, Number],
      required: true,
    },
    pressure: {
      type: [String, Number],
      required: true,
    },
    humidity: {
      type: [String, Number],
      required: true,
    },
    visibility: {
      type: [String, Number],
      required: true,
    },
  },
};
</script>
