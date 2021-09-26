<template>
  <div class="col-xl-2 col-sm-6 col-12 weather-day">
    <div class="day-of-week-weather d-flex justify-content-center">
      {{ week }}
    </div>
    <div class="date-weather-day d-flex justify-content-center">
      {{ date }}
    </div>
    <div class="icon-weather-day d-flex justify-content-center">
      <img
        v-bind:src="require('@/assets/icon/' + icon + '.png')"
        alt="icon weather day"
      />
    </div>

    <div class="temp-weather-day d-flex justify-content-center">
      {{ temp }} <span>°C</span>
    </div>
    <div class="detail-weather-day d-flex justify-content-center">
      {{ description }}
    </div>
  </div>
</template>
<script>
export default {
  name: "WeatherDay",
  props: {
    description: String,
    temp: Number,
    week: String,
    icon: String,
    date: String,
  },
};
</script>

<style>
.icon-weather-day {
  height: 100px;
}
.weather-day {
  padding: 20px;
  border-bottom: 3px solid white;
}
</style>