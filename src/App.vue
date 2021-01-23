<template>
  <div id="main" :class="isDay ? 'day' : 'night'">
    <div class="container col-md-4 col-sm-4 col-lg-4 ">
      <h1 class="title text-center">Weather in</h1>
      <form class="search-location" v-on:submit.prevent="getWeather">
        <input
          type="text"
          id="form-rounded"
          class="form-rounded form-control text-muted  p-4 shadow-sm"
          placeholder="What City?"
          v-model="citySearch"
          autocomplete="off"
        />
      </form>
      <p class="text-center my-3" v-if="cityFound">No city found. Please enter a city name.</p>

      <div class="card my-3 shadow-lg back-card overflow-hidden" >
        <!-- weather animation container -->
        <!-- weather animation container-->
        <div>
          <div icon="sunny" v-if="clearSky">
            <span class="sun"> </span>
          </div>
          <div icon="snowy" v-if="snowy">
            <ul>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
            </ul>
          </div>

          <div icon="stormy" v-if="stormy">
            <span class="cloud"></span>
            <ul>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
            </ul>
          </div>
          <div icon="cloudy" v-if="cloudy">
            <span class="cloud"></span>
            <span class="cloud"></span>
          </div>
          <div icon="nightmoon" v-if="clearNight">
            <span class="moon"></span>
            <span class="meteor"></span>
          </div>
        </div>
        <div>
          <!-- Top of card starts here -->
          <div class="card-top text-center" style="margin-bottom: 15rem">
            <div class="city-name my-3">
              <p>{{ weather.cityName }}</p>
              <span>...</span>
              <p class="">{{ weather.country }}</p>
            </div>
          </div>
          <!-- top of card ends here -->

          <!--card middle body, card body used cos I want to just update the innerHTML -->
          <div class="card-body">
            <!-- card middle starts here -->
            <div class="card-mid">
              <div class="row">
                <div class="col-12 text-center temp">
                  <span> {{ weather.temperature }}&deg;C </span>
                  <p class="my-4">{{ weather.description }}</p>
                </div>
              </div>
              <div class="row">
                <div class="col d-flex justify-content-between px-5 mx-5">
                  <p>
                    <img src="" alt="" />
                    {{ weather.lowTemp }}&deg;C
                  </p>
                  <p>
                    <img src="" alt="" />
                    {{ weather.highTemp }}&deg;C
                  </p>
                </div>
              </div>
            </div>
            <!-- card middle ends here -->

            <!-- card bottom starts here -->
            <div class="card-bottom px-5 py-4 row">
              <div class="col text-center">
                <p>{{ weather.feelsLike }}&deg;C</p>
                <span>Feels like</span>
              </div>
              <div class="col text-center">
                <p>{{ weather.humidity }}%</p>
                <span>humidity</span>
              </div>
            </div>

            <!-- card bottom ends here -->
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cityFound:false,
      visible: false,
      stormy: false,
      cloudy: false,
      clearSky: false,
      clearNight: false,
      snowy: false,
      isDay: true,
      citySearch: "",
      weather: {
        cityName: "...",
        country: "...",
        temperature: 0,
        description: "...",
        lowTemp: "-",
        highTemp: "-",
        feelsLike: "-",
        humidity: "-",
      },
    };
  },
  mounted(){
   this.cityFound = true;
  },
  methods: {
    getWeather: async function () {
      console.log(this.citySearch);
      const key = "5299e83c9cf2fef510fb8ddaa3206ac8";
      const baseURL = `http://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;

      //fetch weather
      try {
        const response = await fetch(baseURL);
      const data = await response.json();
      console.log(data);
      this.citySearch = "";
      this.weather.cityName = data.name;

      this.weather.country = data.sys.country;
      this.weather.temperature = Math.round(data.main.temp);
      this.weather.description = data.weather[0].description;
      this.weather.lowTemp = Math.round(data.main.temp_min);
      this.weather.highTemp = Math.round(data.main.temp_max);
      this.weather.feelsLike = Math.round(data.main.feels_like);
      this.weather.humidity = Math.round(data.main.humidity);

      const timeOfDay = data.weather[0].icon;
      // check for is day of day
      if (timeOfDay.includes("n")) {
        this.isDay = false;
      } else {
        this.isDay = true;
      }
      //check weather animations
      const mainWeather = data.weather[0].main;
      if (mainWeather.includes("Clouds")) {
        this.stormy = false;
        this.cloudy = true;
        this.clearSky = false;
        this.clearNight = false;
        this.snowy = false;
      }
      if (mainWeather.includes("Thunderstorm")) {
        this.stormy = true;
        this.cloudy = false;
        this.clearSky = false;
        this.clearNight = false;
        this.snowy = false;
      }
      if (mainWeather.includes("Clear") && this.isDay) {
        this.stormy = false;
        this.cloudy = false;
        this.clearSky = true;
        this.clearNight = false;
        this.snowy = false;
      }
      if (mainWeather.includes("Clouds") && !this.isDay) {
        this.stormy = false;
        this.cloudy = false;
        this.clearSky = false;
        this.clearNight = true;
        this.snowy = false;
      }
      if (mainWeather.includes("Snow")) {
        this.stormy = false;
        this.cloudy = false;
        this.clearSky = false;
        this.clearNight = false;
        this.snowy = true;
      }

      this.visible = true;
      this.cityFound=false;
      } catch (error) {
        console.log(error);
        this.cityFound = true;
      }

      
    },
  },
};
</script>

<style>
@import "./assets/custom.css";
@import "./assets/animation.css";
</style>
