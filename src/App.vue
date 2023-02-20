<!-- This weather app is created by Abhishek Yadav as a practice in vue.js as suggested mentored by Gaurav Sir -->

<template>
  
  <div id="main" :class="isDay ? 'day' : 'night'">
    <div class="container my-5" style="max-width: 500px; min-width: 360px">
      <h1 class="main-head">Weather in {{ weather.cityName }}</h1>
      <form class="search-location" v-on:submit.prevent="getWeather">
          <input
          
            type="text"
            class="form-control text-muted form-rounded p-4 shadow-sm"
            placeholder="  Please type city name."
            v-model="citySearch"
            autocomplete="off"
          />
      </form> 
      
      <!-- This section for dropdown where i stuck in creating list in dropdown -->
      <section class="dropdown-wrapper">
        <div v-on:click="isVisible = !isvisible" class="selected-item">
          <span>Select City</span>
            <svg
              class="dropdown-icon"
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              width="24"
              height="24"
            >
              <path fill="none" d="M0 0h24v24H0z" />
              <path d="M12 10.828l-4.95 4.95-1.414-1.414L12 8l6.364 6.364-1.414 1.414z"/>
            </svg>
        </div>
        <div v-if="isVisible" class="dropdown-popover">
          <div class="options">
            <ul>
              <!-- <li v-for="(city, index) in filteredCity" v-on:key="`city-${index}`">{{ city }}</li> -->
            </ul>
          </div>
        </div>
      </section>
        <p class="text-center my-3" v-if="cityFound">No city found</p>
      <div class="card rounded my-3 shadow-lg back-card overflow-hidden"  v-if = "visible">

        <!-- city name and country name starts here -->
        <div id="cardview" class="card-top text-center" style="margin-bottom: 15rem">
          <div class="city-name my-3">
            <p>{{ weather.cityName }}</p>
            <span>...</span>
            <p>{{ weather.country }}</p>
          </div>
        </div>
        <!-- city name and country name ends here -->

        <!-- card body start -->
        <div class="card-body">
          <!-- card middle start -->
          <div class="card-mid">
            <div class="row">
              <div class="col-12 text-center temp">
                <span>{{ weather.temperature }}°C</span>
                <p class="my-4">{{ weather.description }}</p>
              </div>
            </div>
            <div class="row">
              <div class="col d-flex justify-content-between px-5 mx-5">
                <p>
                  <img src="./assets/down.svg" alt="down" />
                  {{ weather.lowTemp }}°C
                </p>
                <p>
                  <img src="./assets/up.svg" alt="up" />
                  {{ weather.highTemp }}°C
                </p>
              </div>
            </div>
          </div>
          <!-- card middle end -->

          <!-- card bottom start -->
          <div class="card-bottom px-5 py-4 row">
            <div class="col text-center">
              <p>{{ weather.feelsLike }}°C</p>
              <span>Feels like</span>
            </div>
            <div class="col text-center">
              <p>{{ weather.humidity }}%</p>
              <span>humidity</span>
            </div>
          </div>
          <!-- card bottom end -->

        </div>
        <!-- card body end -->

      </div>
    </div>
  </div>
</template>
 
<script>
import 'bootstrap/dist/css/bootstrap.min.css';
export default {
  data() {
    return {
      searchQuery: "",
      selectedItem: null,
      isVisible: false,

      cityFound: false,
      visible: true,
      stormy: false,
      cloudy: false,
      clearSky: false,
      clearNight: false,
      snowy: false,
      cities: ["New Delhi", "Moscow", "Dubai", "Paris", "London"],
      isDay: true,
      citySearch: "",
      weather: {
        cityName: "Gurgaon",
        country: "IN",
        temperature: 24,
        description: "Sunny",
        lowTemp: "12",
        highTemp: "28",
        feelsLike: "20",
        humidity: "55",
      },
    };
  },

  computed: {
    filteredCity(){
       const query = this.searchQuery.toLowerCase()
       if(this.searchQuery === ""){
        return this.cities;
       }
       return this.cities.filter((city) =>{
        return Object.values(city).some((word) => String(word).toLowerCase().includes(query));
       });
    },
  },
  

  methods: {
    getWeather: async function () {
      console.log(this.citySearch);
      // this.citySearch() = "";
      const key = "20927498238fa45db83392d85885c910";
      const baseURL = `https://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;

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
        this.cities = data;

        const timeOfDay = data.weather[0].icon;

        ///check for time of day
        if (timeOfDay.includes("n")) {
          this.isDay = false;
        } else {
          this.isDay = true;
        }

        this.visible = true;
        this.cityFound = false;
      } 

      catch (error) {
        console.log(error);
        this.cityFound = true;
        this.visible = false;
      }
    },
  },
  getCityWeather(city) {
    this.searchQuery = city;
    this.getWeather();
  },
};

</script>

<style>
@import "./assets/custom.css";
@import "./assets/animation.css";
</style>
