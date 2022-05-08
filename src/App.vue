<template>
  <div id="app" :class="warmCold">
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="请输入地球上的任意地名..."
          v-model="query"
          @keydown.enter="getLocation"
        />
      </div>

      <div class="weather-wrap">
        <div class="location-box">
          <div class="location">{{ city }}, {{ country }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ weather.temp }}℃</div>
          <div class="weather">{{ weather.text }}, {{ weather.windDir }}</div>
        </div>
      </div>
    </main>
  </div>
</template>


<script>
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      // api_key: "a6ef943e9d2c87e6398de3dedfc354c5",
      api_key: "5d8f500960c24cc7b8db77c01f2ea810",
      geo_url: "https://geoapi.qweather.com/v2/city/lookup?location=",
      // url_base: "https://api.openweathermap.org/data/2.5/weather?q=",
      url_base: "https://devapi.qweather.com/v7/weather/now?location=",
      query: "",
      locationID: "",
      city: "北京",
      country: "中国",
      weather: {},
    };
  },
  methods: {
    // api.openweathermap.org/data/2.5/weather?q={city name}&units=metric&appid={API key}
    getLocation() {
      const that = this;
      axios(
        // https://geoapi.qweather.com/v2/city/lookup?location=beij&key=你的KEY
        that.geo_url + that.query + "&key=" + that.api_key
      )
        .then(async (res) => {
          that.locationID = res.data.location[0].id;
          that.city = res.data.location[0].name;
          that.country = res.data.location[0].country;
          await axios(
            // https://devapi.qweather.com/v7/weather/now?location=101010100&key=你的KEY
            that.url_base + that.locationID + "&key=" + that.api_key
          )
            .then((res) => {
              that.weather = res.data.now;
              console.log(that.weather);
            })
            .catch((error) => console.log(error, "error")); // 失败的返回
        })
        .catch((error) => console.log(error, "error")); // 失败的返回
    },
    getDefaultWeather() {
      const that = this;
      axios(
        "https://devapi.qweather.com/v7/weather/now?location=101010100&key=5d8f500960c24cc7b8db77c01f2ea810"
      )
        .then((res) => {
          that.weather = res.data.now;
        })
        .catch((error) => console.log(error, "error")); // 失败的返回
    },
    dateBuilder() {
      let d = new Date();
      d.getFullYear();
      d.getMonth(); // 获取当前月份(0-11,0代表1月)
      d.getDate(); // 获取当前日(1-31)
      let year = d.getFullYear();
      let months = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12];
      let month = months[d.getMonth()];
      let date = d.getDate();

      return year + "年" + month + "月" + date + "日";
    },
  },
  computed: {
    warmCold() {
      return this.weather.temp > 14 ? "warm" : "cold";
    },
  },
  created() {
    //自动加载indexs方法
    this.getDefaultWeather();
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "Microsoft YaHei", "montserrat", sans-serif;
}

#app {
  background-image: url("assets/cold-bg.jpg");
  background-size: cover;
  background-position: center;
  transition: 0.4s;
}

#app .warm {
  background-image: url("assets/warm-bg.jpg");
  background-size: cover;
  background-position: center;
  transition: 0.4s;
}

main {
  min-height: 100vh;
  padding: 35px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.25)
  );
}

.search-box {
  width: 100%;
  margin-bottom: 5px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;

  background-color: rgba(252, 252, 252, 0.75);
  border-radius: 8px;
  transition: 0.4s;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
}

.search-box .search-bar:focus {
  background-color: rgba(252, 252, 252, 0.75);
  box-shadow: 0px 0px 18px rgba(0, 0, 0, 0.25);
}

.location-box {
  margin-top: 20px;
  color: #fff;
  text-align: center;
}

.location-box .location {
  font-size: 40px;
  margin-top: 20px;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  margin-top: 20px;
  font-size: 20px;
  font-style: italic;
  font-weight: 200;
}

.weather-box {
  color: #fff;
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  margin-top: 20px;
  font-size: 100px;
  font-weight: 600;
  text-shadow: 2px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  padding: 10px;
  border: 1px solid rgba(255, 255, 255, 0.05);
  border-radius: 12px;
  box-shadow: 5px 5px rgba(0, 0, 0, 0.15);
}

.weather-box .weather {
  margin-top: 25px;
  font-size: 35px;
  font-weight: 500;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  font-style: italic;
}
</style>