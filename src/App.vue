<template>
  <!--app div-->
  <div class="app flex flex-col items-center">
    <!--header div-->
   <div class="header flex justify-between m-2 bg-[#94beff] items-center pl-4 rounded-lg">
     <!--LOGO and search input-->
     <div class="searchform flex items-center">
       <img class="imgheader" src="./assets/LogoTitle.png" width="50" height="50">
     <input type="text" class="p-2 rounded-lg w-96 ml-4 mr-1 " v-model="city" placeholder="Search City" @keyup.enter="searchcity">
     </div>
     <!--location AND switch button-->
<div class="right flex">
  <button class="m-4 mr-1 bg-sky-500/50 p-1.5 rounded-lg" @click="temp" ><i class="bi bi-thermometer-half text-2xl ml-1 mr-1"></i></button>
  <button class="m-4 mr-1 bg-sky-500/50 p-1.5 rounded-lg" @click="locationcity" ><i class="bi bi-geo-alt-fill text-2xl ml-1 mr-1"></i></button>
</div>
   </div>
    <!--content div-->
    <div class="content rounded-lg bg-[#94beff] flex  flex-col items-center">
      <div class="nameAndtime flex w-full p-2 h-10 justify-between">
        <div class="name flex">
          <h1 class="text-3xl font-semibold font-mono">{{data.name}}</h1>
          <p class="ml-2">{{data.sys.country}}</p>
        </div>
        <h2 class="text-3">{{ time }}</h2>
      </div>
      <!--TEMP div-->
      <div class="topcontent flex  justify-between">
          <div class="temp mt-20 flex flex-col items-center">
            <!--C temp-->
            <div class="cl flex"  v-if="temps == true">
            <h1 class="text-8xl font-semibold font-mono">{{Math.round(data.main.temp -273.15)}}</h1>
              <h1 class="text-3xl">&#8451;</h1>
            </div>
            <!--F temp-->
            <div class="fr flex"  v-if="temps == false">
            <h1 class="text-8xl font-semibold font-mono">{{Math.round(data.main.temp *1.8-459.67)}}</h1>
              <h1 class="text-3xl">&#8457;</h1>
            </div>
            <!--IMG and more...-->
              <h1 class="flex flex-col items-center text-2xl mb-3"><img :src="`https://openweathermap.org/img/wn/${data.weather[0].icon}@2x.png`" width="200" height="200">{{data.weather[0].main}}</h1>
            <div class="maxAndminTemp flex m-3 bg-[#6495ed] p-3 rounded-lg">
              <h2 class="m-1 text-xl" v-if="temps == true"><i class="bi bi-thermometer "></i>{{Math.round(data.main.temp_min -273.15)}}</h2>
              <h2 class="m-1 text-xl" v-if="temps == true"><i class="bi bi-thermometer-high "></i>{{Math.round(data.main.temp_max -273.15)}}</h2>
              <h2 class="m-1 text-xl" v-if="temps == false"><i class="bi bi-thermometer "></i>{{Math.round(data.main.temp_min *1.8-459.67)}}</h2>
              <h2 class="m-1 text-xl" v-if="temps == false"><i class="bi bi-thermometer-high "></i>{{Math.round(data.main.temp_max *1.8-459.67)}}</h2>
            </div>
            <hr>
            <h2 class="m-1 text-xl"><i class="bi bi-droplet-half"></i> {{data.main.humidity}}%</h2>
            <h2 class="m-1 text-xl"><i class="bi bi-wind"></i> {{Math.round(data.wind.speed)}} m/s</h2>
          </div>
      </div>
      <!--days div-->
      <div class="buttomcontent flex flex-row mt-12  bg-[#6495ed] rounded-md">
        <ul id="days"  v-for="day in days">
          <div class="m-1 flex items-center flex-col">
            <img :src="`https://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`" width="80" height="80">
            <h2 class="m-1 text-xl tempindays" v-if="temps == true">{{Math.round(day.temp.day)}}&#8451;</h2>
            <h2 class="m-1 text-xl tempindays" v-if="temps == false">{{Math.round(day.temp.day *1.8+32)}}&#8457;</h2>
          </div>
        </ul>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios"
export default {
  name:'app',
  data(){
    return{
      data:null,
      city:null,
      temps:true,
      time:null,
      days:null
    }
  },
  //CrateApp
  created(){
    axios
        .get('https://api.openweathermap.org/data/2.5/weather?q=tehran&appid=7d13d237d1b54e24d165288bd5e43e04')
        .then(response => (this.data = response.data))
        .catch(error => console.log(error))
       //Date
       this.time = `${new Date().getFullYear()} . ${new Date().getMonth()} . ${new Date().getDate()}`
    setTimeout(()=>{
      //Forecast
      axios
          .get("https://api.openweathermap.org/data/2.5/onecall?lat=35.6944&lon=51.4215&exclude=minutely,hourly,current&units=metric&appid=7d13d237d1b54e24d165288bd5e43e04")
          .then(response => this.days = response.data.daily.slice(1,6))
          .catch(error => console.log(error))
      //UTC Time
      let time = 	Math.floor(new Date().getTime()/1000.0)
     //Rise
      let sunrise = this.data.sys.sunrise
     //Sunset
      let sunset = this.data.sys.sunset
      let header = document.querySelector('.header');
      let content = document.querySelector('.content')
      //day or night
      if (time >=sunrise && time < sunset){
        header.style.backgroundColor = '#94beff'
        content.style.backgroundColor = '#94beff'
      }else{
        header.style.backgroundColor = '#0d3984'
        content.style.backgroundColor = '#0d3984'
      }
  },1000)
  },
  methods:{
    //SearchCity
    searchcity(){
      if (this.city == null){
       alert("لطفا شهر را وارد نمایید :)")
      }else {
        axios
            .get(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&appid=7d13d237d1b54e24d165288bd5e43e04`)
            .then(response => (this.data = response.data))
            .catch(error => console.log(error))
           this.city = null
        setTimeout(()=>{
          //Forecast
          axios
              .get(`https://api.openweathermap.org/data/2.5/onecall?lat=${this.data.coord.lat}&lon=${this.data.coord.lon}&exclude=minutely,hourly,current&units=metric&appid=7d13d237d1b54e24d165288bd5e43e04`)
              .then(response => this.days = response.data.daily.slice(1,6))
              .catch(error => console.log(error))
          //UTC Time
          let time = 	Math.floor(new Date().getTime()/1000.0)
          //Rise
          let sunrise = this.data.sys.sunrise
          //Sunset
          let sunset = this.data.sys.sunset
          let header = document.querySelector('.header');
          let content = document.querySelector('.content')
          //day or night
          if (time >=sunrise && time < sunset){
            header.style.backgroundColor = '#94beff'
            content.style.backgroundColor = '#94beff'
          }else{
            header.style.backgroundColor = '#0d3984'
            content.style.backgroundColor = '#0d3984'
          }
        },1000)
      }
    },
    //Get Location
    locationcity(){
      const getlocation = (position)=>{
        let crd = position.coords;
        axios
           .get(`https://api.openweathermap.org/data/2.5/weather?lat=${crd.latitude}&lon=${crd.longitude}&appid=7d13d237d1b54e24d165288bd5e43e04`)
           .then(response => (this.data = response.data))
           .catch(error => console.log(error))
        setTimeout(()=>{
          //Forecast
          axios
              .get(`https://api.openweathermap.org/data/2.5/onecall?lat=${this.data.coord.lat}&lon=${this.data.coord.lon}&exclude=minutely,hourly,current&units=metric&appid=7d13d237d1b54e24d165288bd5e43e04`)
              .then(response => this.days = response.data.daily.slice(1,6))
              .catch(error => console.log(error))
          //UTC Time
          let time = 	Math.floor(new Date().getTime()/1000.0)
          //Rise
          let sunrise = this.data.sys.sunrise
          //Sunset
          let sunset = this.data.sys.sunset
          let header = document.querySelector('.header');
          let content = document.querySelector('.content')
          //day or night
          if (time >=sunrise && time < sunset){
            header.style.backgroundColor = '#94beff'
            content.style.backgroundColor = '#94beff'
          }else{
            header.style.backgroundColor = '#0d3984'
            content.style.backgroundColor = '#0d3984'
          }
        },1000)
      }
      //run location function
      navigator.geolocation.getCurrentPosition(getlocation)
    },
    //switchTemp(F or C)
    temp(){
       this.temps = !this.temps
    }
  }
}
</script>
<style scoped>
@import url("https://cdn.jsdelivr.net/npm/bootstrap-icons@1.8.1/font/bootstrap-icons.css");
.header{
  width: 98%; height: 70px;
  border: 3px solid #5c8edb;
}
.content{
  width: 98%;
  height: 100vh;
  border: 3px solid #5c8edb;
}
h2,h1,p,i{
  color: white;
  text-shadow: 3px 3px 2px black;
}
@media only screen and (max-width: 700px){
  .buttomcontent{
    width: 70%;
  }
  #days{
    width: 50%;
    margin-left: 0px;
    margin-right: 0px;
    padding: 0px;
  }
  .tempindays{
    font-size: 10px;
  }
}
@media only screen and (max-width: 682px){
  input{
    width: 80%;
  }
  button{
    margin-left: 3px;
    margin-right: 1px;
  }
}
@media only screen and (max-width: 300px){
  .imgheader{
    display: none;
  }
  input{
    margin-left: 0px;
  }
}
</style>
