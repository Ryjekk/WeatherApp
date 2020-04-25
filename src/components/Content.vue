<template>
  <div class="bg-gray-100 md:py-6 md:px-6">
    <div
      class="sm-h-bg md:h-bg md:bg-img bg-cover bg-top bg-cover rounded flex justify-center"
    >
      <div
        class="bg-gray-100 md:w-md-wrapper xl:w-xl-wrapper md:h-wrapper self-center md:shadow-3xl bg-app-img bg-top bg-cover w-full h-full bg-no-repeat md:rounded-xl flex"
      >
        <div class="absolute text-gray-200 px-12 pt-12 w-full">
          <div class="flex justify-between">
            <p class="font-hairline text-2xl capitalize">
              {{ weather_status }}
            </p>
            <div
              class="flex items-center border-b-2 border-indigo-900 py-2 w-5/12"
            >
              <img src="../assets/icons/search.svg" alt="search" />
              <input
                class="appearance-none bg-transparent border-none text-gray-900 placeholder-gray-900 mr-3 px-2 leading-tight focus:outline-none"
                type="text"
                placeholder="Search City"
                v-model="query"
                @keypress="getWeather"
              />
            </div>
          </div>
          <p class="text-5xl">{{ current_temp }} &#778;</p>
        </div>

        <div class="self-end w-full">
          <img src="../assets/images/wave.svg" alt="wave" class="wave" />
          <div
            class="bg-gray-100 md:rounded-b-xl h-temp -mt-4 px-12 flex flex-col justify-around"
          >
            <div class="-mt-6">
              <p class="uppercase tracking-widest font-medium text-lg">
                {{ current_location }}
              </p>
            </div>
            <div
              v-if="tomorrowWeather"
              class="text-gray-600 flex justify-around"
            >
              <div v-for="el in weather_tomorrow" :key="el.id" class="">
                <div class="text-center">
                  {{ el.dt_txt.slice(11, 16) }}
                </div>
                <div>
                  <img
                    :src="
                      require('../assets/icons/' + el.weather[0].icon + '.png')
                    "
                    alt=""
                    class="md:w-16 md:h-16 my-5"
                  />
                </div>
                <div class="text-center">
                  {{ Math.floor(el.main.temp) }} &#778;
                </div>
              </div>
            </div>
            <div v-if="todayWeather" class="text-gray-600 flex justify-around">
              <div v-for="el in weather_today" :key="el.id" class="">
                <div class="text-center">{{ el.dt_txt.slice(11, 16) }}</div>
                <div>
                  <img
                    :src="
                      require('../assets/icons/' + el.weather[0].icon + '.png')
                    "
                    alt=""
                    class="md:w-16 md:h-16 my-5"
                  />
                </div>
                <div class="text-center">
                  {{ Math.floor(el.main.temp) }} &#778;
                </div>
              </div>
            </div>
            <div class="flex flex-row text-gray-600 tracking-widest">
              <div
                class="border-solid border-gray-400 border-b-4 w-1/2 flex justify-center pb-2 cursor-pointer"
                :class="{ 'border-gray-600': todayWeather }"
                @click="showToday"
              >
                Today
              </div>
              <div
                class="border-solid border-gray-400 hover:border-gray-600 border-b-4 w-1/2 flex justify-center cursor-pointer"
                :class="{ 'border-gray-600': tomorrowWeather }"
                @click="showTomorrow"
              >
                Tommorow
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Content",
  methods: {
    async getWeather(event) {
      if (event.key == "Enter") {
        try {
          const results = await axios.get(
            `${this.base_url}forecast?q=${this.query}&units=metric&APPID=${this.api_key}`
          );
          this.weather_today = results.data.list.slice(0, 5);
          this.weather_tomorrow = results.data.list.slice(5, 10);
          this.current_location = results.data.city.name;
          this.weather_status = this.weather_today[0].weather[0].description;
          this.current_temp = Math.floor(this.weather_today[0].main.temp);
          console.log(this.weather_today);
        } catch (err) {
          console.log(err);
        }
      }
    },
    showTomorrow() {
      this.tomorrowWeather = !this.tomorrowWeather;
      this.todayWeather = !this.todayWeather;
    },
    showToday() {
      this.tomorrowWeather = !this.tomorrowWeather;
      this.todayWeather = !this.todayWeather;
    }
  },
  data() {
    return {
      api_key: process.env.VUE_APP_API_KEY,
      base_url: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather_today: [],
      weather_tomorrow: [],
      weather_status: "Weather now",
      current_temp: "-",
      current_location: "",
      tomorrowWeather: false,
      todayWeather: true
    };
  }
};
</script>
