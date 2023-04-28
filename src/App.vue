<template>
<div id="app" :class="typeof weather.main != 'undefined' ? background : 'warm' ">
  <main>
    <div class="search-box">
      <input type="text" 
      placeholder="Search..."
       class="search-bar"
       v-model="query"
       @keypress="fetchWeather">
    </div>
     <div class="weather-wrap" v-if="typeof weather.main != 'undefined' && weather.main.temp != 'undefined' ">
      <div class="location-box">
        <div class="location">
          {{ weather.name }} , {{ weather.sys.country }}
        </div>
        <div class="date">
          {{dateBuilder()}}
        </div>
      </div>
      <div class="weather-box">
        <div class="temp">{{ Math.round(weather.main.temp )}}°c</div>
          <div class="weather">
              {{ weather.weather[0].main }}
          </div>
      </div>
      <div class="current">
    <div class="hi-low">{{ Math.round(weather.main.temp_min) }}<span>°c</span> / {{Math.round(weather.main.temp_max)}}<span>°c</span></div>
</div>
    </div>
   <div v-else-if="typeof weather.main === 'undefined' && notFound "> 
      <transition name="notFound" appear>
      <div class="notFound">
        {{query}} doesn't exists!
      </div>
      </transition>
    </div>
  </main>
</div>
</template>

<script>


export default {
  name: 'App',
  components: {},
  data(){
    return{
      background:'cold',
      api_key :process.env.API_KEY,
      url_base : 'https://api.openweathermap.org/data/2.5/',
      query :'',
      weather : {},
      notFound:false
    }
  },
  created() {
    if(navigator.geolocation){
     navigator.geolocation.getCurrentPosition(
       position=>{
         console.log(position.coords);
       },
       err=>{
         console.log(err)
       },{enableHighAccuracy: true}
     ); 
    }
    else{
      console.log('sth went wrong')
    }
    },
  methods:{
    fetchWeather (e) {
      if(e.key == "Enter")
      {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
        .then(res => {
          return res.json();
        }).then(this.setResults)
        .catch(err=>{
          console.log(err)
          this.notFound = true
        });
      }
    },
    setResults (results) {
      this.weather = results;
      const temp = this.weather.main.temp

     
       if( temp > 21 ){
         this.background = 'warm'
               }
        else if( temp > 12 ){
           this.background = 'clear'
        }
        else {
            this.background = 'cold'
        }
      this.notFound=true;
      setTimeout(() => (this.notFound= false), 6000);
    },
    dateBuilder (){
      let d = new Date();
     let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    }
  }
}
</script>

<style>
*
{
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body
{
font-family: 'montserrat', san-serif;
}
#app.cold
{
  background-image: url('./assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
#app.warm
{
  background-image: url('./assets/sunny-bg.jpg');
   background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
#app.clear{
background-image: url('./assets/clear-bg.jpg');
 background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

main{
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(rgba(0,0,0,0.25) , rgba(0,0,0,0.75));
}
.search-box
{
margin-bottom: 30px;
width: 100%;
}
.search-box  .search-bar
{
border-radius: 0 15px 0 15px;
color: #313131;
display: block;
padding: 15px;
width: 100%;
font-size: 20px;
border: none;
background: none;
appearance: none;
background-color: rgba(255,255,255,0.25);
box-shadow:0 0 8px rgba(0, 0, 0,0.25);
transition:  0.4s;
}
.search-box .search-bar:focus
{
    outline: none;
  background-color: rgba(255,255,255,0.5);
  box-shadow:0 0 16px rgba(0, 0, 0,0.5);
  border-radius: 15px 0px 15px 0px;
}
.location-box .location{
color: #fff;
font-size: 32px;
font-weight: 500;
text-align: center;
text-shadow: 1px 3px rgba(0, 0, 0,0.25);
}
.notFound
{
  margin-top: 15%;
color: #fff;
font-size: 32px;
font-weight: 500;
text-align: center;
text-shadow: 1px 3px rgba(0, 0, 0,0.25);
animation: bibile 0.5s ease;

}
.location-box .date
{
color: #fff;
font-size: 20px;
font-weight: 200;
font-style: italic;
text-align: center;
padding: 5px;
}
.weather-box
{
  text-align: center;
}
.weather-box .temp
{
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0,0.25);
  background-color: rgba(255,255,255,0.25);
  margin:30px 0 ;
  border-radius: 15px;
  box-shadow: 3px 6px rgba(0, 0, 0,0.25);
}
.weather-box .weather
{
  color: #fff;
  font-size: 45px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0,0.25);
}
.current .hi-low {
  color: #fff;
  font-size: 20px;
  font-weight: 500;
  text-shadow: 1px 2px rgba(0, 0, 0, 0.4);
  text-align: center;
  padding-top: 30px;
}

@keyframes bibile{
  0%{
    transform: translateY(-60px);
    opacity: 0;
  }
  50%{
    transform: translateY(0px);
    opacity: 1;
  }
  60%{transform: translateX(8px);}
  70%{transform: translateX(-8px);}
  80%{transform: translateX(4px);}
  90%{transform: translateX(-4px);}
  100%{transform: translateX(0);}
}
</style>
