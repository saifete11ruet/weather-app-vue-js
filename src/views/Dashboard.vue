<template>
  <v-app id="dashboard">
    <v-main style="padding: 0">
      <v-container fluid>
        <!-- start navbar section -->
        <v-app-bar fixed app color="primary" dark>
          <v-spacer></v-spacer>
          <div class="header-menu">
            <router-link to="/">Dashboard</router-link>
            <router-link to="/signup/">Signup</router-link>
          </div>
        </v-app-bar>
        <!-- end navbar section -->

        <!-- start Main section -->
        <v-row justify="start" align="center" class="weather-details">
          <!-- start current weather section -->
          <v-col class="currentWeather" cols="12" sm="12" md="4" lg="3">
            <!-- start search form -->
            <v-form ref="form" v-model="valid" lazy-validation>
              <div class="search">
                <v-text-field
                  light
                  name="city"
                  type="text"
                  label="Search by City"
                  v-model="city"
                  :rules="cityRules"
                  required
                  outlined
                  dense
                  class="search-input"
                  @keydown.enter.prevent="showCityWeather"
                ></v-text-field>
                <v-btn
                  class="search-btn"
                  depressed
                  color="primary"
                  @click="showCityWeather"
                  :disabled="!this.city"
                  :loading="loading"
                >
                  Search
                </v-btn>
              </div>
            </v-form>
            <!-- end search form -->
            <!-- start alert box for error -->
            <v-alert
              v-model="alertFailed"
              border="left"
              close-text="Close Alert"
              color="red"
              dark
              dismissible
            >
              {{ weatherError }}
            </v-alert>
            <!-- end alert box -->

            <!-- start current weather data (coming from api)-->
            <div
              v-if="weather && typeof weather === 'object'"
              class="weather-info"
            >
              <div class="weather-date-time">
                <div class="weather-icon">
                  <v-img
                    v-if="
                      weather.current &&
                      weather.current.condition &&
                      weather.current.condition.icon
                    "
                    max-width="250"
                    :src="weather.current.condition.icon"
                    :alt="weather.current.condition.text"
                  ></v-img>
                </div>
                <div class="weather-date-time-tx">
                  <h4>Today</h4>
                  <h5 v-if="weather.location && weather.location.localtime">
                    {{ weather.location.localtime.split(" ")[1] }}
                  </h5>
                  <h3 v-if="weather.location && weather.location.localtime">
                    {{ weekday[new Date(weather.location.localtime).getDay()] }}
                  </h3>
                </div>
              </div>
              <div class="weather-temp">
                <h1
                  v-if="weather.current && weather.current.temp_c !== undefined"
                >
                  {{ weather.current.temp_c }}&deg;C
                </h1>

                <h2 v-if="weather.location && weather.location.name">
                  {{ weather.location.name }}
                </h2>
                <h3 v-if="weather.location && weather.location.country">
                  {{ weather.location.country }}
                </h3>
              </div>
              <v-divider class="mb-6"></v-divider>
              <div class="condition">
                <h1
                  v-if="
                    weather.current &&
                    weather.current.condition &&
                    weather.current.condition.text
                  "
                >
                  {{ weather.current.condition.text }}
                </h1>
                <h4
                  v-if="
                    weather.current && weather.current.precip_mm !== undefined
                  "
                >
                  Precipitation: {{ weather.current.precip_mm }} %
                </h4>
                <h4
                  v-if="
                    weather.current && weather.current.humidity !== undefined
                  "
                >
                  Humidity: {{ weather.current.humidity }} %
                </h4>
                <h4
                  v-if="
                    weather.current && weather.current.wind_kph !== undefined
                  "
                >
                  Wind: {{ weather.current.wind_kph }} km/h
                </h4>
              </div>
            </div>
            <!-- end current weather data -->
          </v-col>
          <!-- end current weather section -->

          <!-- start weather forecast section -->
          <v-col class="forecast" cols="12" sm="12" md="8" lg="9">
            <div class="forecast-title">Weather Forecast</div>

            <v-row
              v-if="
                weather &&
                weather.forecast &&
                weather.forecast.forecastday &&
                weather.forecast.forecastday.length
              "
            >
              <v-col
                cols="12"
                sm="12"
                md="4"
                lg="3"
                v-for="forecast in weather.forecast.forecastday"
                :key="forecast.date"
              >
                <v-card max-width="500" class="forecast-item">
                  <v-list-item two-line class="pa-0">
                    <v-list-item-content>
                      <v-list-item-title class="text-h5" v-if="forecast.date">
                        {{ weekday[new Date(forecast.date).getDay()] }}
                      </v-list-item-title>
                    </v-list-item-content>
                  </v-list-item>

                  <div class="forecast-item-middle">
                    <div
                      class="forecast-item-middle-tx"
                      v-if="
                        forecast.hour &&
                        forecast.hour.length &&
                        forecast.hour[0].temp_c !== undefined
                      "
                    >
                      {{ forecast.hour[0].temp_c }}&deg;C
                    </div>
                    <div class="forecast-item-middle-img">
                      <v-img
                        width="92"
                        v-if="
                          forecast.hour &&
                          forecast.hour.length &&
                          forecast.hour[0].condition &&
                          forecast.hour[0].condition.icon &&
                          forecast.hour[0].condition.text
                        "
                        :src="forecast.hour[0].condition.icon"
                        :alt="forecast.hour[0].condition.text"
                      ></v-img>
                    </div>
                  </div>

                  <div class="forecast-condition">
                    <h4
                      v-if="
                        forecast.hour &&
                        forecast.hour.length &&
                        forecast.hour[0].condition &&
                        forecast.hour[0].condition.icon &&
                        forecast.hour[0].condition.text
                      "
                    >
                      {{ forecast.hour[0].condition.text }}
                    </h4>
                  </div>

                  <v-divider class="mt-4"></v-divider>

                  <div class="min-max-temp">
                    <h4
                      v-if="
                        forecast.day && forecast.day.maxtemp_c !== undefined
                      "
                    >
                      Max: {{ forecast.day.maxtemp_c }}&deg;C
                    </h4>
                    <v-spacer></v-spacer>

                    <h4
                      v-if="
                        forecast.day && forecast.day.mintemp_c !== undefined
                      "
                    >
                      Min: {{ forecast.day.mintemp_c }}&deg;C
                    </h4>
                  </div>
                </v-card>
              </v-col>
            </v-row>

            <div
              v-if="
                weather &&
                weather.forecast &&
                weather.forecast.forecastday &&
                weather.forecast.forecastday.length
              "
              class="weather-forecast"
            ></div>
          </v-col>
          <!-- end weather forecast section -->
        </v-row>
        <!-- end main section -->
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
  import axios from "axios";
  export default {
    name: "Dashboard",
    data: () => ({
      valid: true,
      loading: false,
      city: "",
      cityRules: [(v) => !!v || "City is required"],
      defaultCity: "Dhaka",
      weather: "",
      weekday: [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ],
      alertFailed: false,
      weatherError: "",
    }),
    created() {
      this.showCityWeather(); // Showing weather update for last searched city or Default city
    },
    methods: {
      showCityWeather() {
        if (this.city) {
          this.$refs.form.validate();

          this.loading = true;
        }
        // Call api to get weather update for searched city or last searched city or default city
        axios
          .get(
            `${process.env.VUE_APP_WEATHER_API_URL}/forecast.json?key=${
              process.env.VUE_APP_WEATHER_API_KEY
            }&q=${
              this.city ||
              localStorage.getItem("lastSearchedCity") ||
              this.defaultCity
            }&days=3&hour=11`
          )
          .then((response) => {
            this.alertFailed = false;
            this.weather = response.data;
            this.loading = false;
            localStorage.setItem("lastSearchedCity", this.city); // saving city name for further use
          })
          .catch((error) => {
            this.alertFailed = true;
            this.loading = false;
            if (
              error.response &&
              error.response.status &&
              error.response.status == 400 &&
              error.response.data &&
              error.response.data.error &&
              error.response.data.error.code &&
              error.response.data.error.code == 1006
            ) {
              this.weatherError = "No City Found matching this name";
            } else {
              this.weatherError = "Failed to provide weather update";
            }
          });
      },
    },
  };
</script>

<style scoped>
  /** Navbar */
  .header-menu {
    display: flex;
  }
  .header-menu a {
    margin: 0 10px;
    color: white;
    font-size: 16px;
    text-decoration: none;
  }
  .header-menu a:hover {
    color: #cbe5ff;
  }

  /** Weather section */
  .weather-details {
    background-color: #f7f6f9;
  }

  /** Current Weather */
  .currentWeather {
    min-height: 100vh;
    background-color: #fff;
    position: relative;
  }
  .search {
    display: flex;
    margin-top: 24px;
  }
  .search-input {
    border-top-right-radius: 0;
    border-bottom-right-radius: 0;
    transform: translateX(-1px);
  }
  .search-btn {
    height: 40px !important;
    border-top-left-radius: 0;
    border-bottom-left-radius: 0;
    transform: translateX(-1px);
  }
  .weather-info {
    text-align: center;
  }
  .weather-date-time {
    width: 100%;
  }
  .weather-icon {
    width: 40px;
    display: inline-block;
    vertical-align: top;
  }
  .weather-date-time-tx {
    display: inline-block;
  }
  .weather-date-time-tx h4 {
    font-size: 24px;
  }
  .weather-date-time-tx h5 {
    font-size: 18px;
  }
  .weather-date-time-tx h3 {
    color: #6a6969;
  }
  .weather-temp {
    padding: 24px;
  }
  .weather-temp h1 {
    font-size: 80px;
    font-weight: 400;
    color: #474a52;
    text-align: center;
    line-height: 1.4;
  }
  .weather-temp h2 {
    font-size: 26px;
    color: #656566;
    font-weight: 500;
  }
  .weather-temp h3 {
    font-size: 18px;
    color: #656566;
    font-weight: 400;
  }
  .condition {
    margin-left: 44px;
    display: inline-block;
    text-align: left;
  }
  .condition h1 {
    color: #5cabf9;
    margin-bottom: 8px;
  }
  .condition h4 {
    line-height: 1.8;
    color: #6a6969;
  }

  /* Weather Forecast */
  .forecast {
    min-height: 100vh;
  }
  .forecast-title {
    font-size: 44px;
    padding: 24px;
    color: #343434;
  }
  .weather-forecast {
    display: flex;
  }
  .forecast-item {
    padding: 24px;
  }
  .forecast-item-middle {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .forecast-item-middle-tx {
    display: inline-block;
    font-size: 48px;
    vertical-align: top;
    margin-top: 14px;
    color: #5cabf9;
  }
  .forecast-item-middle-img {
    display: inline-block;
  }
  .forecast-condition h4 {
    font-size: 16px;
    color: #626262;
  }
  .min-max-temp {
    display: flex;
    margin-top: 16px;
  }

  /** Media queries */
  @media only screen and (max-width: 1300px) {
    .forecast-item {
      padding: 12px;
    }
  }
  @media only screen and (max-width: 945px) {
    .forecast-item {
      padding: 24px;
    }
  }
  @media only screen and (max-width: 600px) {
    .forecast-title {
      font-size: 32px;
    }
  }
</style>
