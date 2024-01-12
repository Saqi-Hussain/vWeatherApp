<template>
  <!-- main div for background png  -->

  <div class="py-10 lg:py-5 text-white">
    <!-- inner div as a main container of weather app -->
    <transition name="mainFade">
      <div
        class="w-[20rem] md:w-[35rem] lg:w-[50rem] overflow-hidden rounded-xl flex flex-col lg:flex-row flex-wrap gap-4 items-center lg:justify-center mx-auto lg:bg-yellow-900 lg:bg-opacity-50 lg:py-3 bg-black bg-opacity-45 py-2 mt-[30rem] lg:mt-0"
        v-if="state.loading == false"
      >
        <div
          class="leftContainer lg:w-[40%] rounded-xl overflow-hidden flex flex-col justify-center items-center p-2 space-y-2 bg-gradient-to-r via-yellow-600 to-yellow-900 pt-5"
        >
          <div class="w-full flex flex-col gap-5">
            <input
              name="text"
              type="text"
              class="outline-dotted outline-1 outline-orange-400 rounded-2xl pl-5 text-xs py-[3px] bg-black w-full"
              placeholder="search for place "
              @keypress.enter="searchForSpecificPlace"
              v-model="state.searchedCountry"
            />
            <transition name="fade">
              <div
                v-if="state.errorMsgToUser != ''"
                class="bg-red-600 text-center w-56 absolute mt-7 ml-11 text-lg font-bold"
              >
                {{ state.errorMsgToUser }}
              </div>
            </transition>
          </div>
          <div class="flex space-x-2">
            <div
              class="cursor-pointer hover:text-amber-900 transition delay-75 ease-in-out text-sm font-semibold"
              @click="getLahoreWeather"
            >
              Karachi
            </div>
            <div
              class="cursor-pointer hover:text-amber-900 transition delay-75 ease-in-out text-sm font-semibold"
              @click="getIslamabadWeather"
            >
              Islamabad
            </div>
            <div
              class="cursor-pointer hover:text-amber-900 transition delay-75 ease-in-out text-sm font-semibold"
              @click="getMyLocationWeather"
            >
              My location
            </div>
          </div>
          <div
            class="innerLeftContainers flex flex-col justify-center items-center space-y-5"
          >
            <div
              class="upper flex flex-col justify-center items-center p-2 rounded-2xl space-y-4"
            >
              <div v-if="state.currentTemperature == ''">Loading...</div>
              <div v-else class="mainShow">
                <div class="text-xl font-semibold text-center">
                  {{ state.currentLocationName }}
                </div>
                <div class="text-4xl font-bold text-center">
                  {{ state.currentTemperature.toFixed() }}
                  {{ state.currentTemperatureUnit }}
                </div>
                <!-- <div>
                <img :src="state.link" alt="" class="mx-auto" />
              </div> -->
              </div>
              <div class="mainShow text-xl">
                {{ state.currentState }}
              </div>
              <div class="mainShow text-center text-xs text-slate-300">
                Today there is {{ state.currentDescription }} all around
              </div>
            </div>
            <div class="lower grid grid-cols-2 gap-4 p-2 w-[18rem]">
              <div
                class="p-2 flex flex-col bg-yellow-900 bg-opacity-50 rounded-xl"
              >
                <div class="text-xl font-semibold">Feels like</div>
                <div class="font-semibold mt-2">
                  {{ state.currentFeelsLike }}
                </div>
              </div>
              <div class="p-2 flex bg-yellow-900 bg-opacity-50 rounded-xl">
                <!-- forwind -->
                <div>
                  <div class="text-xl font-semibold">Wind</div>
                  <div class="flex gap-3">
                    <div class="grid place-items-center">
                      <div class="font-semibold">
                        {{ state.currentWindSpeed }}
                      </div>
                    </div>
                    <div class="text-xs mt-2 text-slate-300">
                      <div>Kmph</div>
                      <div>wind</div>
                    </div>
                  </div>
                  <div class="flex gap-3">
                    <div class="grid place-items-center">
                      <div class="font-semibold">
                        {{ state.currentGust }}
                      </div>
                    </div>
                    <div class="text-xs mt-2 text-slate-300">
                      <div>Kmph</div>
                      <div>gust</div>
                    </div>
                  </div>
                </div>
                <!-- <div class="grid place-items-center">direction logo</div> -->
              </div>
              <div
                class="p-2 flex flex-col bg-yellow-900 bg-opacity-50 rounded-xl"
              >
                <div class="text-xl font-semibold">Visiblity</div>
                <div class="font-semibold mt-2">
                  {{ state.currentVisibility / 1000 }} <span>Km</span>
                </div>
              </div>
              <div
                class="p-2 flex flex-col bg-yellow-900 bg-opacity-50 rounded-xl"
              >
                <div class="text-xl font-semibold">Humidity</div>
                <div class="font-semibold mt-2">{{ state.currenHumdity }}%</div>
              </div>
            </div>
          </div>
        </div>
        <div
          class="rightContainer w-[18rem] md:w-[30rem] lg:w-[55%] rounded-xl flex flex-col p-2 justify-center items-center space-y-2 overflow-hidden"
        >
          <div
            class="flex flex-col w-full rounded-xl gap-2 p-2 bg-amber-900 bg-opacity-70"
          >
            <div class="flex text-sm text-yellow-500">Hourly Forcast</div>
            <div class="border-t-2 border-t-yellow-600"></div>
            <div class="flex space-x-1 overflow-y-auto p-1 m-1">
              <div
                v-for="(forecast, index) in getHourlyForecast()"
                :key="index"
              >
                <div
                  class="px-2 mb-2 w-20 rounded-lg pt-2 pl-3"
                  :class="{
                    'shadow-[0_0_5px_2px] shadow-yellow-300': index == 0,
                  }"
                >
                  <div class="text-[9px]">
                    {{ new Date(forecast.dt_txt).toLocaleTimeString() }}
                  </div>
                  <div class="font-semibold">
                    {{ forecast.main.temp.toFixed() }}°C
                  </div>
                  <img
                    :src="getWeatherIconUrl(forecast.weather[0].icon)"
                    alt=""
                  />
                </div>
              </div>
            </div>
          </div>
          <div
            class="flex flex-col w-full rounded-xl gap-2 p-2 bg-amber-900 bg-opacity-70"
          >
            <div class="text-sm text-yellow-500">5 days Forcast</div>
            <div class="border-t-2 border-t-yellow-600"></div>
            <div class="flex overflow-y-auto space-x-1 p-1 m-1">
              <div
                v-for="(forecast, index) in getNext5DaysForecast()"
                :key="index"
              >
                <div
                  class="px-2 mb-2 w-20 rounded-lg pt-2 pl-3 space-y-1"
                  :class="{
                    'shadow-[0_0_5px_2px] shadow-yellow-300': index == 0,
                  }"
                >
                  <div v-if="index == 0" class="font-semibold">Today</div>
                  <div v-else class="font-semibold">
                    {{ getDayName(new Date(forecast.dt_txt)) }}
                  </div>
                  <div class="text-[9px]">
                    {{ new Date(forecast.dt_txt).toLocaleDateString() }}
                  </div>
                  <div class="font-semibold">
                    {{ forecast.main.temp.toFixed() }}°C
                  </div>
                  <img
                    :src="getWeatherIconUrl(forecast.weather[0].icon)"
                    alt=""
                  />
                </div>
              </div>
            </div>
          </div>
          <!-- <div class="border flex w-full rounded-xl justify-center ">
          <div class="border p-2 flex flex-col">
            <div>UV Index</div>
            <div>3</div>
            <div>Moderate</div>
            <div><input type="range" disabled /></div>
            <div>use sun protection</div>
          </div>
        </div> -->
        </div>
      </div>
      <div class="loading-message" v-else>Loading...</div>
    </transition>
  </div>
</template>

<script>
export default {
  name: "weather",
};
</script>

<script setup>
import axios from "axios";
import { onMounted, reactive } from "vue";

const state = reactive({
  country: "",
  currentTemperature: 0,
  currentTemperatureUnit: 0,
  currentState: "",
  currentDescription: "",
  currentFeelsLike: "",
  currentIcon: "",
  link: "",
  currentVisibility: "",
  currenHumdity: "",
  currentLocationName: "",
  forecastList: [],
  currentWindSpeed: "",
  currenWindDirection: "",
  currentGust: "",
  currentLat: "",
  currentLong: "",
  searchedCountry: "",
  errorMsgToUser: "",
  loading: false,
});

const timeConvertion = () => {
  const unixTimeSeconds = 1614721600;
  const unixTimeMilliseconds = unixTimeSeconds * 1000;
  const date = new Date(unixTimeMilliseconds);
};
const getWeatherIconUrl = (iconCode) => {
  return `http://openweathermap.org/img/w/${iconCode}.png`;
};

const getNext5DaysForecast = () => {
  const processedDates = [];

  return state.forecastList.filter((forecast) => {
    const forecastDate = new Date(forecast.dt_txt).toLocaleDateString();

    if (!processedDates.includes(forecastDate)) {
      processedDates.push(forecastDate);

      // Now 'formattedDate' contains the date in the desired format

      return true;
    }
    return false;
  });
};

const getDayName = (date) => {
  const options = { weekday: "long" };
  return date.toLocaleDateString("en-US", options);
};

const getHourlyForecast = () => {
  const processedHours = [];

  return state.forecastList.filter((forecast) => {
    const forecastHour = new Date(forecast.dt_txt).getHours();
    if (!processedHours.includes(forecastHour)) {
      processedHours.push(forecastHour);
      return true;
    }
    return false;
  });
};

const getLahoreWeather = async () => {
  state.loading = true;
  state.country = "karachi";
  localStorage.setItem("recent_searched_place", "karachi");

  getWeatherInfo();
};
const getIslamabadWeather = () => {
  state.loading = true;
  state.country = "islamabad";
  localStorage.setItem("recent_searched_place", "islamabad");
  getWeatherInfo();
};

const getWeatherInfo = async () => {
  try {
    let result = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?q=${state.country}
      &units=metric&APPID=69cc60af695295acb9266bf788271741`
    );

    state.currentTemperature = result.data.main.temp;
    state.currentTemperatureUnit = "°C";
    state.currentState = result.data.weather[0].main;
    state.currentDescription = result.data.weather[0].description;
    state.currentIcon = result.data.weather[0].icon;
    state.currentFeelsLike = result.data.main.feels_like;
    state.link = `http://openweathermap.org/img/w/${state.currentIcon}.png`;
    state.currentVisibility = result.data.visibility;
    state.currenHumdity = result.data.main.humidity;
    state.currentLocationName = result.data.name;

    let forecaseResult =
      await axios.get(`https://api.openweathermap.org/data/2.5/forecast?q=${state.country}
      &units=metric&APPID=69cc60af695295acb9266bf788271741`);

    state.forecastList = forecaseResult.data.list;
    state.currentWindSpeed = forecaseResult.data.list[0].wind.speed;
    state.currentGust = forecaseResult.data.list[0].wind.gust;

    timeConvertion();
    state.loading = false;
  } catch (error) {
    if (error.response) {
      state.errorMsgToUser = error.response.data.message;
    } else {
      state.errorMsgToUser = error.message;
    }
    setTimeout(() => {
      state.errorMsgToUser = "";
    }, 2000);

    state.loading = false;
  }
};
const success = async (position) => {
  state.currentLat = position.coords.latitude;

  state.currentLong = position.coords.longitude;

  try {
    let result = await axios.get(
      `https://api.openweathermap.org/geo/1.0/reverse?lat=${state.currentLat}&lon=${state.currentLong}&limit=1&appid=69cc60af695295acb9266bf788271741`
    );

    state.country = result.data[0].name;
    localStorage.setItem("recent_searched_place", result.data[0].name);
    state.loading = true;
    getWeatherInfo();
  } catch (error) {}
};

function fail() {
  // Could not obtain location
  console.log("fail");
}

const getMyLocationWeather = () => {
  console.log("my location");
  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(success, fail);
  } else {
    alert("Sorry, your browser does not support geolocation services.");
  }
};
const searchForSpecificPlace = () => {
  state.loading = true;
  state.country = state.searchedCountry;
  localStorage.setItem("recent_searched_place", state.searchedCountry);
  getWeatherInfo();
  state.searchedCountry = "";
};

onMounted(async () => {
  if (localStorage.getItem("recent_searched_place")) {
    state.country = localStorage.getItem("recent_searched_place");
  } else {
    state.country = "florida";
  }
  state.loading = true;
  getWeatherInfo();
});
</script>

<style scoped>
::-webkit-scrollbar {
  height: 0.5em;
}

::-webkit-scrollbar-track {
  box-shadow: inset 0 0 6px rgba(214, 218, 0, 0.3);
  border-radius: 10px;
}

::-webkit-scrollbar-thumb {
  background-color: #824515;
  outline: 1px solid #ca8a04;
  border-radius: 10px;
}
::-webkit-scrollbar-thumb:hover {
  background-color: #412a18;
  outline: none;
}
.loading-message {
  text-align: center;
  font-size: 2rem;
  color: #333; /* Adjust color as needed */
  padding: 10px;
  font-weight: bold;
}
.fade-enter-from {
  opacity: 0;
  transform: translateY(-68px);
}

.fade-enter-to {
  opacity: 1;
  transform: translateY(0);
}

.fade-enter-active {
  transition: all 2s ease;
}

.fade-leave-from {
  opacity: 1;
  transform: translateY(0);
}
.fade-leave-to {
  opacity: 0;
  transform: translateY(-60px);
}
.fade-leave-active {
  transition: all 0.3s ease;
}
.mainFade-enter-from {
  opacity: 0;
}
.mainFade-enter-to {
  opacity: 1;
}
.mainFade-enter-active {
  transition: all 1.3s ease;
}
.mainFade-leave-from {
  opacity: 1;
}
.mainFade-leave-to {
  opacity: 0;
}
.mainFade-leave-active {
  transition: all 0.1s ease;
}
</style>
