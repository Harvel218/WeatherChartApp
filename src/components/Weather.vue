<template>
  <div class="content">
    <header class="content--title">
      <h1>
        Get your current weather data, from any place you can&nbsp;imagine!
      </h1>
    </header>
    <nav class="content__search">
      <input
        class="content__search--input"
        @keyup.enter="getCurrentCityWeather"
        placeholder="Search for city..."
        type="search"
        name="search"
        v-model="city"
        :disabled="loading"
      />
      <button
        class="content__search--button"
        @click="getCurrentCityWeather"
        :disabled="loading"
      >
        Find
      </button>
    </nav>
    <div class="content__loader" v-if="loading">
      <Loading> </Loading>
    </div>

    <div class="content__error" v-if="error">
      {{ errorMessage }}
    </div>
    <div class="content__two-column" v-if="loaded">
      <CityInfo
        :city="cityWeatherData.name"
        :country="cityWeatherData.sys.country"
        :time="getCurrentWeatherTime"
        :temp="cityWeatherData.main.temp"
        :sensedTemp="cityWeatherData.main.feels_like"
        :weatherDesc="cityWeatherData.weather"
        :windSpeed="cityWeatherData.wind.speed"
        :windDirection="getWindDirection"
        :pressure="cityWeatherData.main.pressure"
        :humidity="cityWeatherData.main.humidity"
        :visibility="cityWeatherData.visibility"
      ></CityInfo>

      <DailyCityInfo
        :dailyData="weeklyWeatherData.daily"
        :timezone="cityWeatherData.timezone"
        :timezoneOffset="timezoneOffset"
      ></DailyCityInfo>
    </div>

    <div class="chart__container" v-if="loaded">
      <line-chart
        :chart-labels="chartLabels"
        :chart-data="chartData"
      ></line-chart>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import LineChart from "@/components/LineChart.vue";
import CityInfo from "@/components/CityInfo.vue";
import DailyCityInfo from "@/components/DailyCityInfo.vue";
import Loading from "@/components/Loading.vue";

export default {
  components: {
    LineChart,
    CityInfo,
    DailyCityInfo,
    Loading,
  },

  data() {
    return {
      city: null,
      API_KEY: process.env.VUE_APP_API_KEY,
      cityWeatherData: null,
      weeklyWeatherData: null,
      loaded: false,
      loading: false,
      error: false,
      errorMessage: null,
      chartLabels: null,
      chartData: null,
    };
  },

  methods: {
    getCurrentCityWeather: function () {
      this.loaded = false;
      this.loading = true;
      this.error = false;
      if (this.city === null || this.city === "" || this.city === "undefined") {
        this.error = true;
        this.errorMessage = "Entered Data is required";
        return;
      }
      axios
        .get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.city}&lang=en&units=metric&appid=${this.API_KEY}`
        )
        .then((response) => {
          this.cityWeatherData = response.data;
          this.getWeeklyWeather();
        })
        .catch((err) => {
          this.loading = false;
          this.loaded = false;
          this.error = true;
          this.errorMessage = err.response.data.message || err;
          console.error(err);
        });
    },

    getWeeklyWeather: function () {
      axios
        .get(
          `https://api.openweathermap.org/data/2.5/onecall?lat=${this.cityWeatherData.coord.lat}&lon=${this.cityWeatherData.coord.lon}&lang=en&units=metric&exclude=current,minutely,alerts&appid=${this.API_KEY}`
        )
        .then((response) => {
          this.weeklyWeatherData = response.data;

          this.chartLabels = this.weeklyWeatherData.hourly.reduce(
            (result, item, index) => {
              switch (true) {
                case index === 0:
                  result.push("Time now");
                  break;
                case index < 13:
                  result.push(
                    new Date(
                      item.dt * 1000 +
                        this.weeklyWeatherData.timezone_offset * 1000 +
                        this.timezoneOffset
                    ).toLocaleTimeString([], {
                      hour: "2-digit",
                      minute: "2-digit",
                    })
                  );
                  break;
              }
              return result;
            },
            []
          );

          this.chartData = this.weeklyWeatherData.hourly.reduce(
            (result, item, index) => {
              if (index < 13) {
                result.push(item.temp);
              }
              return result;
            },
            []
          );

          this.loading = false;
          this.loaded = true;
        });
    },
  },

  computed: {
    getWindDirection: function () {
      let degrees = this.cityWeatherData.wind.deg;
      const directions = [
        "north",
        "northeast",
        "east",
        "southeast",
        "south",
        "southwest",
        "west",
        "northwest",
      ];
      degrees = (degrees * 8) / 360;
      degrees = Math.round(degrees, 0);
      degrees = (degrees + 8) % 8;

      return directions[degrees];
    },

    getCurrentWeatherTime: function () {
      const date = new Date(
        this.cityWeatherData.dt * 1000 +
          this.cityWeatherData.timezone * 1000 +
          this.timezoneOffset
      ).toLocaleDateString("en-GB", { day: "numeric", month: "short" });

      const time = new Date(
        this.cityWeatherData.dt * 1000 +
          this.cityWeatherData.timezone * 1000 +
          this.timezoneOffset
      ).toLocaleTimeString([], {
        hour: "2-digit",
        minute: "2-digit",
      });

      const fullDate = date + " " + time;

      return fullDate;
    },

    timezoneOffset: function () {
      return new Date().getTimezoneOffset() * 1000 * 60;
    },
  },
};
</script>

<style scoped lang="scss">
.content {
  min-height: 100%;
  width: calc(100% - 48px);
  max-width: 850px;
  padding: 24px;
  margin: 0 auto;
  background-color: rgba(256, 256, 256, 0.85);

  &__error {
    padding: 16px;
    margin: 24px -24px -24px;
    font-size: 18px;
    color: #ff3b3b;
    font-weight: 600;
    border: 3px solid #ff3b3b;
  }

  &__loader {
    width: 100%;
    display: flex;
    justify-content: center;
    padding: 24px 0;
  }

  &--title {
    padding-bottom: 24px;
    color: #007d5f;

    h1 {
      margin: 0;
      font-size: 32px;
    }
  }

  &__two-column {
    display: flex;
    justify-content: space-around;
    margin: 24px 0;

    @media (max-width: 575px) {
      flex-direction: column;
    }

    section {
      width: 42%;

      @media (max-width: 575px) {
        width: calc(100% - 32px);
      }
    }
  }

  &__search {
    display: inline-block;
    width: auto;
    margin: 0 auto;
    padding: 16px;
    border-radius: 16px;
    background-color: #007d5f;
    position: relative;
    max-width: 100%;

    &--input {
      background-color: #007d5f;
      border: 1px solid transparent;
      outline: none;
      width: 100%;
      font-size: 18px;
      padding-right: 60px;
      color: #fff;
      letter-spacing: 1.25px;

      &::placeholder {
        color: rgba(256, 256, 256, 0.75);
      }

      &::-webkit-search-cancel-button {
        -webkit-appearance: none;
      }
    }

    &--button {
      position: absolute;
      font-size: 18px;
      outline: none;
      border: none;
      border-radius: 0 16px 16px 0;
      background-color: #27876f;
      font-weight: 600;
      color: #fff;
      right: 0;
      top: 0;
      height: 100%;
      padding: 0 12px;
      cursor: pointer;
    }
  }
}
</style>