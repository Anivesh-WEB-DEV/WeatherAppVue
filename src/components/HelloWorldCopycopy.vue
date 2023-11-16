<script>
import { ref, onMounted } from "vue";
import axios from "axios";
import clouds from "../assets/1clouds.png";
import clearImage from "../assets/clear.png";
import mist from "../assets/mist.png";
import rain from "../assets/rain.png";
import drizzle from "../assets/drizzle.png";
import smoke from "../assets/smokeco2.png";
import HumidityImage from "../assets/Humidity1.png";
import WindyImage from "../assets/windy.png";

export default {
  setup() {
    const data = ref({
      celcius: 10,
      name: "London",
      min: 5,
      max: 20,
      weather: "Cloudy",
      country: "GB",
      humidity: 10,
      speed: 2,
      image: clouds,
    });
    const name = ref("");
    const error = ref("");

    const handleClick = () => {
      const apiUrl = `https://api.openweathermap.org/data/2.5/weather?q=${name.value}&appid=16f1db8b9338a87d910f1a98966da8e5&&units=metric`;

      axios
        .get(apiUrl)
        .then((res) => {
          let imagePath = clouds;
          if (res.data.weather[0].main == "Clouds") {
            imagePath = clouds;
          } else if (res.data.weather[0].main == "Clear") {
            imagePath = clearImage;
          } else if (res.data.weather[0].main == "Rain") {
            imagePath = rain;
          } else if (res.data.weather[0].main == "Drizzle") {
            imagePath = drizzle;
          } else if (res.data.weather[0].main == "Mist") {
            imagePath = mist;
          } else if (res.data.weather[0].main == "Smoke") {
            imagePath = smoke;
          }

          data.value = {
            celcius: res.data.main.temp,
            name: res.data.name,
            humidity: res.data.main.humidity,
            speed: res.data.wind.speed,
            image: imagePath,
            weather: res.data.weather[0].description,
            min: res.data.main.temp_min,
            max: res.data.main.temp_max,
            country: res.data.sys.country,
          };
          error.value = "";
        })
        .catch((err) => {
          if (err.response && err.response.status == 404) {
            error.value = "! Invalid City Name";
          } else {
            error.value = "";
          }
          console.log(err);
        });
    };

    onMounted(() => {
      handleClick();
    });
    return {
      data,
      name,
      error,
      handleClick,
      HumidityImage,
      WindyImage,
    };
  },
};
</script>

<template>
  <div class="container">
    <div class="wrapper">
      <h1 class="heading">Weather App</h1>
      <input
        type="text"
        spellcheck="false"
        placeholder="Enter City Name"
        v-model="name"
        @keydown.enter="handleClick"
      />
      <button @click="handleClick">
        <font-awesome-icon icon="fa-solid fa-search" class="icon" />
      </button>
      <div class="error">
        <p>{{ error }}</p>
      </div>
      <div class="winfo">
        <img :src="data.image" alt="" class="weatherimg" />
        <p>{{ data.weather }}</p>
        <h1>{{ Math.round(data.celcius) }}°c</h1>
        <p>
          Min↓ {{ Math.round(data.min) }}°c &nbsp; &nbsp; Max↑
          {{ Math.round(data.max) }}°c
        </p>
        <h2>{{ data.name }} - {{ data.country }}</h2>
        <div class="details">
          <div class="col">
            <img :src="HumidityImage" alt="HumidityImage" />
            <div class="humidity">
              <p>{{ Math.round(data.humidity) }}%</p>
              <p>Humidity</p>
            </div>
          </div>
          <div class="col">
            <img :src="WindyImage" alt="Windy" />
            <div class="wind">
              <p>{{ Math.round(data.speed) }} km/h</p>
              <p>Wind</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  width: 573px;
  margin: auto;
  height: 680px;
  padding-top: 10px;
}

.wrapper {
  background: linear-gradient(130deg, #5656eb, #0505c7);
  border-radius: 10px;
  color: #fff;
  text-align: center;
  height: 650px;
}

.heading {
  padding: 24px 24px 0px 24px;
  margin: 24px 24px -5px 24px;
  font-size: 58px;
  font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen,
    Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
}

.icon {
  margin: 2px -11px 1px -10px;
  font-size: 20px;
}

button {
  width: 51px;
  height: 48px;
  margin: 23px 10px 36px -5px;
  border-radius: 50%;
  background-color: white;
  border: 1px solid white;
}

input {
  border: none;
  outline: 0;
  color: black;
  margin: 24px 15px 24px 24px;
  height: 46px;
  width: 293px;
  border-radius: 25px;
  font-size: 23px;
  font-weight: 500;
  text-align: center;
}

.images img {
  height: 222px;
  width: 277px;
}

.winfo img {
  height: 141px;
  margin-top: 6px;
  width: 149px;
}

.winfo h1 {
  font-size: 70px;
  font-weight: 500;
  margin-top: -15px;
}

.winfo h2 {
  font-size: 40px;
  font-weight: 400;
  margin-top: -103px;
}

.winfo p {
  font-size: 25px;
  font-weight: 500;
  margin-top: 2px;
}

.details {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0px 20px;
  margin-top: 72px;
}

.details .col {
  display: flex;
  align-items: center;
  text-align: left;
}

.details .col img {
  width: 45px;
  margin-right: 10px;
  color: white;
  height: auto;
}

.humidity,
.wind {
  font-size: 27px;
}

.humidity p,
.wind p {
  margin: 0px;
}

.error {
  color: rgb(255, 114, 114);
  text-align: left;
  margin-left: 210px;
  margin-top: -43px;
  margin-bottom: -29px;
  font-size: 18px;
}

@media (max-width: 440px) {
  .container {
    width: fit-content;
    height: 130vh;
    /* padding: 20px 0px; */
  }

  .weather {
    margin: 0px 20px;
    margin-top: 100px;
  }

  .heading {
    display: block;
  }
}
</style>
