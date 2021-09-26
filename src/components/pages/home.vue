<template>
  <div class="container-fluid home">
    <div class="home-weather">
      <div class="row row-weather-current">
        <div class="col-xl-12 col-weather-current">
          <div>
            <input-search
              v-bind:placeholder="'Tìm kiếm vị trí khác...'"
              v-on:onChange="handleChangeSearch"
            ></input-search>
          </div>
          <div class="location-weather-current d-flex justify-content-center">
            {{ nameCity }}
          </div>
          <div class="date-weather-current d-flex justify-content-center">
            {{
              getWeekDays(weatherCurrent.dt) + ", " + getDate(weatherCurrent.dt)
            }}
          </div>
          <div class="icon-weather-current d-flex justify-content-center">
            <img
              v-bind:src="
                require('@/assets/icon/' +
                  weatherCurrent.weather[0].icon +
                  '.png')
              "
              alt="icon weather current"
            />
          </div>
          <div class="temp-weather-current d-flex justify-content-center">
            {{ mathRound(weatherCurrent.temp.day) }} <span>°C</span>
          </div>
          <div class="humidity-weather-current d-flex justify-content-center">
            Độ Ẩm: <span> {{ weatherCurrent.humidity }}%</span>
          </div>
          <div class="speed-weather-current d-flex justify-content-center">
            Tốc Độ Gió: <span> {{ weatherCurrent.wind_speed }} km</span>
          </div>
          <div class="detail-weather-current d-flex justify-content-center">
            <p>{{ weatherCurrent.weather[0].description }}</p>
          </div>
        </div>
      </div>
      <div class="row row-weather-day">
        <weather-day
          v-for="(item, index) of weatherWeeks"
          v-bind:key="index"
          v-bind:date="getDate(item.dt)"
          v-bind:description="item.weather[0].description"
          v-bind:icon="item.weather[0].icon"
          v-bind:temp="mathRound(item.temp.day)"
          v-bind:week="getWeekDays(item.dt)"
        ></weather-day>
      </div>
    </div>
  </div>
</template>
<script>
import WeatherDay from "../weather-day.vue";
import weather from "../../services/weather.js";
import InputSearch from "../input-search.vue";
import axios from "axios";
export default {
  name: "Home",
  data() {
    const lat = 0;
    const lon = 0;
    const nameCity = "";
    const weatherCurrent = {};
    const weatherWeeks = [];
    return {
      lat,
      lon,
      nameCity,
      weatherCurrent,
      weatherWeeks,
    };
  },
  components: {
    WeatherDay,
    InputSearch,
  },
  methods: {
    getLocation() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          (position) => {
            this.lat = position.coords.latitude;
            this.lon = position.coords.longitude;
            this.getWeatherWeeks(this.lat, this.lon);
            this.getCityName(this.lat, this.lon);
          },
          (error) => {
            this.$toast.error("Đây không phải vị trí của bạn, xin hãy cấp quyền vị trí...", {
              position: "bottom-right",
              timeout: 3048,
              closeOnClick: true,
              pauseOnFocusLoss: true,
              pauseOnHover: true,
              draggable: true,
              draggablePercent: 0.6,
              showCloseButtonOnHover: false,
              hideProgressBar: true,
              closeButton: "button",
              icon: true,
              rtl: false,
            });
            this.getWeatherWeeks(this.lat, this.lon);
            this.getCityName(this.lat, this.lon);
          }
        );
      }
    },
    getWeatherWeeks(lat, lon) {
      const call = async () => {
        const res = await weather({
          method: "get",
          url: `/data/2.5/onecall?&exclude=current,minutely,hourly&appid=a5baaf83acd61ead8f2b525537740766&units=metric&lang=vi&lat=${lat}&lon=${lon}`,
        });
        this.weatherCurrent = res.data.daily[0];
        this.weatherWeeks = res.data.daily.filter(
          (item, index) => index != 0 && index != 7
        );
      };
      call();
    },
    getCityName(lat, lon) {
      axios
        .get(
          `https://api.bigdatacloud.net/data/reverse-geocode-client?latitude=${lat}&longitude=${lon}&localityLanguage=vi`
        )
        .then((res) => {
          this.nameCity = res.data.locality + " - " + res.data.city;
        });
    },
    mathRound(number) {
      return Math.round(number);
    },
    handleChangeSearch(data) {
      this.getLocationByNameCity(data.inputText);
    },
    getLocationByNameCity(nameCity) {
      axios
        .get(
          `https://api.openweathermap.org/geo/1.0/direct?appid=a5baaf83acd61ead8f2b525537740766&q=${nameCity}`
        )
        .then((res) => {
          if (res.data[0]) {
            this.lat = res.data[0].lat;
            this.lon = res.data[0].lon;
            this.getCityName(this.lat, this.lon);
            this.getWeatherWeeks(this.lat, this.lon);
          } else {
            this.$toast.error("Không thể tìm thấy địa chỉ", {
              position: "bottom-right",
              timeout: 3048,
              closeOnClick: true,
              pauseOnFocusLoss: true,
              pauseOnHover: true,
              draggable: true,
              draggablePercent: 0.6,
              showCloseButtonOnHover: false,
              hideProgressBar: true,
              closeButton: "button",
              icon: true,
              rtl: false,
            });
          }
        });
    },
    getDate(dateGetTime) {
      const date = new Date(dateGetTime * 1000);
      return `${date.getDate()}/${date.getMonth() + 1}/${date.getFullYear()}`;
    },
    getWeekDays(dateGetTime) {
      const date = new Date(dateGetTime * 1000);
      let weekDays;

      switch (date.getDay() + 1) {
        case 2:
          weekDays = "Thứ 2";
          break;
        case 3:
          weekDays = "Thứ 3";
          break;
        case 4:
          weekDays = "Thứ 4";
          break;
        case 5:
          weekDays = "Thứ 5";
          break;
        case 6:
          weekDays = "Thứ 6";
          break;
        case 7:
          weekDays = "Thứ 7";
          break;
        case 1:
          weekDays = "Chủ Nhật";
          break;
        default:
      }
      return weekDays;
    },
  },
  created() {
    this.getLocation();
  },
};
</script>

<style scoped>
.home-weather {
  border-radius: 5px;

  background-color: #0093e9;
  background-image: linear-gradient(160deg, #0093e9 0%, #80d0c7 100%);
  color: white;
  box-shadow: 4px 3px 10px;
  text-shadow: 1px 1px 3px rgb(0, 0, 0);
}
.row-weather-current {
  margin: 0;
  border-bottom: 3px solid white;
}
.location-weather-current {
  font-size: 3vw;
}
.icon-weather-current img {
  height: 150px;
}
.temp-weather-current {
  font-size: 40px;
}
.row-weather-day {
  margin: 0;
}
</style>