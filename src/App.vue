<template>
 <body class="bg-[url('../img/background.jpg')] bg-cover bg-no-repeat bg-fixed">
    <div class="bg-red-500 mx-auto m-10 max-w-4xl w-11/12 rounded-2xl overflow-auto ">
      
      <div class="flex m-4 justify-between flex-wrap">
        <div>
          <h1 class="text-4xl">{{cityInfo.name}}, {{cityInfo.country}} {{displayLat(cityInfo.lat)}} {{displayLon(cityInfo.lon)}}</h1>
          <p class="ml-4">The weather for today and for the upcoming week</p>
        </div>

        <form class="my-2 sm:my-auto flex" @submit.prevent="findCity(city)">
          <input type="text" placeholder="Search..." class="text-xl h-min rounded-3xl px-3 py-2 w-56" v-model="city">
          <div class="my-auto mx-2">
            <img src="./assets/img/search.png" width="30" height="30" @click="findCity(city)">
          </div>
        </form>
      </div>

      <div class="m-2 rounded-2xl border-4 border-black relative overflow-hidden" style="background: white;" id="days" v-for="(day, index) in weatherInfo" :key="day">
        <h1 class="relative top-1 left-2 text-xl md:absolute m-2 z-10">{{getDayNum(index)}} {{getMonthNum()}} {{getDayName(index)}}</h1>

          <div class="flex justify-between flex-wrap relative">     

        <div class="flex items-center flex-wrap w-1/4 justify-evenly md:w-1/5 flex-col md:flex-row ">
          <img src="./assets/img/cloud.png" class="w-10 h-10 md:w-16 md:h-16">
          <p class="text-center md:text-lg">{{getMainWeather(index)}}</p>
        </div>

        <div class="flex items-center flex-wrap w-1/4 justify-evenly md:w-1/5 flex-col md:flex-row ">
          <img src="./assets/img/temp.png" class="w-10 h-10 md:w-16 md:h-16">
          <p class="text-center md:text-lg ">{{getTemperature(index)}}</p>
        </div>

        <div class="flex items-center flex-wrap w-1/4 justify-evenly md:w-1/5 flex-col md:flex-row ">
          <img src="./assets/img/wind.png" class="w-10 h-10 md:w-16 md:h-16">
          <p class="text-center md:text-lg">{{getWindSpeed(index)}}</p>
        </div>

        <div class="flex items-center flex-wrap w-1/4 justify-evenly md:w-1/5 flex-col md:flex-row">
          <img src="./assets/img/rainy.png" class="w-10 h-10 md:w-16 md:h-16">
          <p class="text-center md:text-lg">{{getDescription(index)}}</p>
        </div>

        <div class="flex md:items-center md:flex-col md:w-1/5 md:justify-evenly m-auto items-center">
          <img src="./assets/img/sunrise.png" width="40" >
          <p class="text-center md:text-lg ">{{getSunRise(index)}}</p>

          <img src="./assets/img/sunset.png" width="40">
          <p class="text-center md:text-lg">{{getSunSet(index)}}</p>
        </div>
          </div>
      </div>

      </div>

  </body>
</template>

<script>
import './assets/tailwind.css'
export default {
  data(){
    return{
      api: "a54f30d388402d8679ffab61d9458745",
      city: null,
      cityInfo: {
        country: null,
        lat: null,
        lon: null,
        name: null
      },
      weatherInfo: null
    }
  },
  methods: {
    findCity(city){
      fetch(`http://api.openweathermap.org/geo/1.0/direct?q=${city}&limit=1&appid=${this.api}`)
        .then((response) => response.json())
        .then((location) => this.fetchCity(location));

    },
    fetchCity(location){
      this.cityInfo.country = location[0].country
      this.cityInfo.lat = location[0].lat
      this.cityInfo.lon = location[0].lon
      this.cityInfo.name = location[0].name
      fetch(`https://api.openweathermap.org/data/2.5/onecall?lat=${this.cityInfo.lat}&lon=${this.cityInfo.lon}&exclude=alerts,hourly,minutely,current&appid=${this.api}`)
      .then((response) => response.json())
      .then((weather) => this.fetchWeather(weather))
    },
    fetchWeather(weather){
      this.weatherInfo = weather.daily
    },
    displayLat(lat){
      let num = Math.round(lat*100)/100
      return (num > 0 ? `${num} N` : `${num} S`)
    },
    displayLon(lon){
      let num = Math.round(lon*100)/100
      return (num > 0 ? `${num} E` : `${num} W`)
    },
    getDayName(index){
      return (new Date(2022,2,new Date().getDate() + index)).toLocaleString('en-us', {weekday: 'long'})
    },
    getDayNum(index){
      return `${new Date(new Date().getFullYear(), new Date().getMonth() + 1, new Date().getDate() + index).getDate()}.`
    },
    getMonthNum(){
      return `${new Date().getMonth() + 1}.`
    },
    getTemperature(index){
      return `${Math.round((this.weatherInfo[index].temp.day - 273)*10)/10}°C`
    },
    getSunRise(index){
      let date = new Date(this.weatherInfo[index].sunrise * 1000)
      let hours = date.getHours()
      let minutes = "0" + date.getMinutes();
      let formattedTime = hours + ':' + minutes.substr(-2);
      return formattedTime
    },
    getSunSet(index){
      let date = new Date(this.weatherInfo[index].sunset * 1000)
      let hours = date.getHours()
      let minutes = "0" + date.getMinutes();
      let formattedTime = hours + ':' + minutes.substr(-2);
      return formattedTime
    },
    getWindSpeed(index){
      return `${Math.round(this.weatherInfo[index].wind_speed)}m/s`
    },
    getMainWeather(index){
      let clouds = this.weatherInfo[index].clouds 
      if(clouds < 20){
        return "Azure"
      }else if(clouds < 40){
        return "Clear"
      }else{
        return "Cloudy"
      }
    },
    getDescription(index){
      return this.weatherInfo[index].weather[0].main
    },
  },
  mounted() {
    this.findCity("Praha")
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Orbitron:wght@500&display=swap');
body{
  background: url(./assets/img/background.jpg);
    background-repeat: no-repeat;
  background-attachment: fixed;
  background-size: cover;
}
#days{
  font-family: 'Orbitron', sans-serif;
}
</style>
