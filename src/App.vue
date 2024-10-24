<template>
  <div id="app">
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keyup.enter="fetchWeather"
        />
      </div>

      <div class="weather-card">
        <!-- Weather data fetched: Display dynamic weather info -->
        <div v-if="weather.main">
          <div class="city-time">
            {{ cityTime.hours }}:{{ cityTime.minutes }} <span>{{ cityTime.ampm }}</span>
          </div>
          <div class="city-details">
            <h2>{{ weather.name }}</h2>
            <p>{{ weather.sys.country }}</p>
          </div>

          <div class="weather">
            <div>
              <img :src="weatherIcon" alt="Weather icon" />
            </div>
          </div>

          <div class="date-weather">
            <div class="temperature">
              {{ Math.round(weather.main.temp) }}<span>&deg;</span>
            </div>
            <div class="date">
              {{ dateBuilder() }}
            </div>
          </div>
        </div>

        <!-- Default UI: No weather data fetched -->
        <div v-else>
          <div class="city-time">11:44<span>AM</span></div>
          <div class="city-details">
            <h2>Nairobi</h2>
            <p>KE</p>
          </div>

          <div class="weather">
            <div>
              <img src="./assets/cloud.png" alt="Default weather" />
            </div>
          </div>

          <div class="date-weather">
            <div class="temperature">17<span>&deg;</span></div>
            <div class="date">01 April</div>
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      query: "",
      weather: {}, // Initially empty, weather data will be stored here
      localTime: "", // To hold the city time
    };
  },
  methods: {
    fetchWeather() {
      const api_key = "36df309c5e261213fe8c1651d1e57a1e"; // Replace with your OpenWeather API key
      const url_base = "https://api.openweathermap.org/data/2.5/";

      if (this.query) {
        fetch(`${url_base}weather?q=${this.query}&units=metric&APPID=${api_key}`)
          .then((res) => res.json())
          .then(this.setResults)
          .catch((error) => {
            console.error("Error fetching weather:", error);
          });
      }
    },
    setResults(results) {
      this.weather = results;
      this.updateCityTime();
    },
    updateCityTime() {
      const utcTime = Date.now(); // Get current UTC time
      const cityOffset = this.weather.timezone * 1000; // Convert city timezone offset to milliseconds
      const localTime = new Date(utcTime + cityOffset); // Calculate local city time
      this.localTime = localTime;
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December"
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday"
      ];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
  },
  computed: {
    cityTime() {
      if (!this.localTime) {
        return { hours: "00", minutes: "00", ampm: "AM" }; // Default time if no local time is available
      }
      let hours = this.localTime.getUTCHours();
      let minutes = this.localTime.getUTCMinutes();
      let ampm = hours >= 12 ? "PM" : "AM";
      hours = hours % 12;
      hours = hours ? hours : 12; // The hour '0' should be '12'
      minutes = minutes < 10 ? "0" + minutes : minutes;

      return {
        hours: hours < 10 ? "0" + hours : hours, // Ensure hours are two digits
        minutes: minutes,
        ampm: ampm
      };
    },
    weatherIcon() {
      if (!this.weather.weather) return "./assets/default-weather.png"; // Default image if no weather data is available

      const weatherCondition = this.weather.weather[0].main.toLowerCase();

      // Map weather conditions to images
      const weatherImages = {
        clear: "./assets/clear-sunny.png",
        clouds: "./assets/cloud.png",
        rain: "./assets/rain.png",
        snow: "./assets/snow.png",
        drizzle: "./assets/rain.png",
        thunderstorm: "./assets/rain.png",
        mist: "./assets/cloud.png",
        haze: "./assets/cloud.png",
        fog: "./assets/cloud.png",
        smoke: "./assets/smoke.png",
        dust: "./assets/dust.png",
        sand: "./assets/sand.png",
      };

      // Fallback to default if condition not found
      return weatherImages[weatherCondition] || "./assets/default-weather.png";
    }
  }
};
</script>


<style scoped>
@import url("https://fonts.googleapis.com/css2?family=DM+Sans:ital,opsz,wght@0,9..40,100..1000;1,9..40,100..1000&display=swap");
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "DM Sans", sans-serif;
}
main {
  min-height: 100vh;
  padding: 20px;
  position: relative;
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

#app {
  background-image: url("./assets/back-space.png");
  background-size: cover;
  background-position: bottom;
  vertical-align: middle;
  transition: 0.4s;
}

.search-bar {
  display: block;
  width: 100%;
  padding: 20px 20px;
  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background-color: rgba(255, 255, 255, 0.512);
  border-radius: 4px;
  transition: 0.4s;
  box-shadow: rgba(0, 0, 0, 0.2) 0px 18px 50px -10px;
}

.search-bar:focus {
  background-color: rgba(252, 252, 252, 0.897);
  border-radius: 4px;
  box-shadow: rgba(0, 0, 0, 0.25) 0px 25px 50px -12px;
}

.weather-card {
  border-radius: 4px;
  padding: 16px;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #222127;
  width: 360px;
  height: 500px;
}
.city-time {
  height: 100px;
  width: 200px;
  background: #00000000;
  position: absolute;
  top: -100px;
  left: -200px;
  color: #313131;
  padding: 10px;
  font-size: 50px;
  font-weight: 600;
}
.city-time span {
  font-size: 20px !important;
}
.city-details h2 {
  font-size: 38px;
  font-weight: 600;
  color: #c3c4c9;
}
.city-details p {
  color: #7f7f80;
  font-size: 20px;
  font-weight: 600;
}
.weather {
  width: 328px;
  height: 200px;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 38px;
  font-weight: 600;
  color: #c3c4c9;
}
img {
  width: 700px;
}
.date-weather {
  width: 328px;
  position: absolute;
  bottom: 16px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.temperature {
  font-size: 120px;
  font-weight: 900;
  color: #acadaf;
}
.temperature span {
  color: #7f7f80;
}
.date {
  font-size: 20px;
  font-weight: 900;
  color: #7f7f80;
}
</style>
