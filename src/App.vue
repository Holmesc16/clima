<template>
  <div id="app">
    <!-- NAVBAR -->
    <header>
      <nav>
        <div class="nav-wrapper">
          <div class="row">
            <div class="col 812">
              <a href="#" data-target="sidenav-1" class="left sidenav-trigger"><i class="material-icons">menu</i></a>

              <div class="logo"><a href="#" data-target="sidenav-2"
                  class="right sidenav-trigger show-on-medium-and-up"><span>{{ description }}</span><img id="logo"
                    src="./assets/logo.svg" class="svg right" /></a></div>
            </div>
          </div>
        </div>
      </nav>
    </header>


    <!-- LEFT SIDEBAR	 -->
    <ul id="sidenav-1" class="sidenav sidenav-fixed">
      <div class="input-field col s4">
        <i class="material-icons prefix"><img src="./assets/search.svg" width="32" alt="search"></i>
        <input id="icon_prefix" ref="input" type="text" class="validate" @keyup.enter="organize()">
        <label for="icon_prefix">Destination</label>
        <a @click="organize()" class="waves-effect waves-light btn-small" style="background-color:#5474fb;"
          id="btn-go">GO</a>
      </div>
      <!-- <div class="row">
        <div class="current-location col s12 center-align">
            <h2 v-if="location.length > 0">{{ location}}</h2>
        </div>
      </div> -->
      <div class="row">
        <div class="current-temp col s12 left-align">
          <div class="row">
            <div class="col s12">
              <h1 class="temp_h1" v-if="currentWeather.temp">{{ currentWeather.temp }}&deg;<sup></sup></h1>
              <h4 class="summary_h4" v-if="currentWeather.summary">{{currentWeather.summary}}</h4>
            </div>
          </div>
          <div class="row" v-if="currentWeather.todayHighLow.todayTempHigh !== ''">
            <div class="col s12">
              <h5><i
                  class="large material-icons yellow-text">arrow_drop_up</i>{{ currentWeather.todayHighLow.todayTempHigh }}&deg;<sup></sup>&nbsp;&nbsp;&nbsp;<span
                  class="infoText">{{ currentWeather.todayHighLow.todayTempHighTime }}</span>
              </h5>
              <h5><i
                  class="large material-icons blue-text">arrow_drop_down</i>{{ currentWeather.todayHighLow.todayTempLow }}&deg;<sup></sup>&nbsp;&nbsp;&nbsp;<span class="infoText"
                  >{{ currentWeather.todayHighLow.todayTempLowTime }}</span>
              </h5>
            </div>
          </div>
          <div class="row" v-if="currentWeather.time !== ''">
          <div class="col 12">
                  <img src="./assets/calendar.svg" class="svg calendar" width="92" alt="calendar">
                  <div>
                    <h6>{{ currentWeather.time }}</h6>
                  </div>
            </div>
          </div>
          <div class="row" v-if="currentWeather.full_location !== ''">
              <div class="col 12">                
                  <i class="large material-icons blue-text location">location_on</i>
                  <div class="locationInfo">
                    <h6>{{ currentWeather.full_location }}</h6>
                    <h6 class="infoText">Latitude: {{ lat }}</h6>
                    <h6 class="infoText">Longitude: {{ lng }}</h6>
                  </div>
                </div>
              </div>
        </div>
      </div>
    </ul>


    <main>
        <div class="row">
          <div class="col s2"><!--show-on-medium-and-up--></div>
          <div class="col s10">
          <dashboard :tempVar="tempVar" :highlights="highlights"></dashboard>
        </div>
        </div>
    </main>

  </div>
</template>


<script>
  import Content from './components/Content.vue'
  let axios = require('axios')
  let moment = require('moment')
  let momentTimezone = require('moment-timezone')

  export default {
    name: 'app',
    components: {
      'dashboard': Content
    },
    data() {
      return {
        description: 'An Interactive Bilingual Weather Dashboard',
        hitEnterKey: false,
        weatherDetails: false,
        location: '',
        lat: '', // raw lat from google maps api response
        lng: '', // raw lng from google maps api response,
        completeWeatherApi: '',
        rawWeatherData: '',
        currentWeather: {
          full_location: '',
          formatted_lat: '',
          formatted_lng: '',
          time: '',
          temp: '',
          todayHighLow: {
            todayTempHigh: '',
            todayTempHighTime: '',
            todayTempLow: '',
            todayTempLowTime: ''
          },
          summary: '',
          possibility: ''
        },
        tempVar: {
          tempToday: []
        },
        highlights: {
          uvIndex: '',
          visibility: '',
          windStatus: {
            windSpeed: '',
            windDirection: '',
            derivedWindDirection: ''
          },
        }
      }
    },
    mounted: async function() {
      this.location = 'Dallas'
      await this.organize()
    },
    methods: {
      //utils
      titleCase: function (str) {
        str = str.toLowerCase().split(' ');
        for (var i = 0; i < str.length; i++) {
          str[i] = str[i].charAt(0).toUpperCase() + str[i].slice(1);
        }
        return str.join(' ');
      },
      unixToHuman: function (timezone, timestamp) {
        var decipher = new Date(timestamp * 1000);
        var human = moment(decipher)
          .tz(timezone)
          .format('llll');
        var timeArray = human.split(' ');
        var timeNumeral = timeArray[4];
        var timeSuffix = timeArray[5];
        var justTime = timeNumeral + ' ' + timeSuffix;
        var monthDateArray = human.split(',');
        var monthDate = monthDateArray[1].trim();
        return {
          fullTime: human,
          onlyTime: justTime,
          onlyMonthDate: monthDate
        };
      },
      fahToCel: function (tempInFahrenheit) {
        var tempInCelcius = Math.round((5 / 9) * (tempInFahrenheit - 32));
        return tempInCelcius;
      },
      milibarToKiloPascal: function (pressureInMilibar) {
        var pressureInKPA = pressureInMilibar * 0.1;
        return Math.round(pressureInKPA);
      },
      mileToKilometer: function (miles) {
        var kilometer = miles * 1.60934;
        return Math.round(kilometer);
      },
      deriveWindDir: function (windDir) {
        var wind_directions_array = [
          { minVal: 0, maxVal: 30, direction: 'N' },
          { minVal: 31, maxVal: 45, direction: 'NNE' },
          { minVal: 46, maxVal: 75, direction: 'NE' },
          { minVal: 76, maxVal: 90, direction: 'ENE' },
          { minVal: 91, maxVal: 120, direction: 'E' },
          { minVal: 121, maxVal: 135, direction: 'ESE' },
          { minVal: 136, maxVal: 165, direction: 'SE' },
          { minVal: 166, maxVal: 180, direction: 'SSE' },
          { minVal: 181, maxVal: 210, direction: 'S' },
          { minVal: 211, maxVal: 225, direction: 'SSW' },
          { minVal: 226, maxVal: 255, direction: 'SW' },
          { minVal: 256, maxVal: 270, direction: 'WSW' },
          { minVal: 271, maxVal: 300, direction: 'W' },
          { minVal: 301, maxVal: 315, direction: 'WNW' },
          { minVal: 316, maxVal: 345, direction: 'NW' },
          { minVal: 346, maxVal: 360, direction: 'NNW' }
        ];
        var wind_direction = '';
        for (var i = 0; i < wind_directions_array.length; i++) {
          if (
            windDir >= wind_directions_array[i].minVal &&
            windDir <= wind_directions_array[i].maxVal
          ) {
            wind_direction = wind_directions_array[i].direction;
          }
        }
        return wind_direction;
      },

      //actions
      clearInput() {
        this.$refs.input.value = ''
      },
      clearTodayTemp() {
        this.tempVar.tempToday = []
      },
      setHitEnterKeyTrue() {
        return this.hitEnterKey = true
      },
      detectEnterKeypress() {
        let input = this.$refs.input
        input.addEventListener('keyup', (event) => {
          event.preventDefault()
          let enterKeyCode = 13
          if (event.keyCode === enterKeyCode) {
            this.setHitEnterKeyTrue();
          }
        })
      },
      locationEntered() {
        let input = this.$refs.input
        if (input.value === '') {
          this.location = 'Dallas'
        }
        else {
          this.location = this.titleCase(input.value)
        }
        this.clearInput()
        this.clearTodayTemp()
      },
      //get Coordinates
      getCoordinates() {
        this.locationEntered()
        let coords,
          _location = this.location
        let geocoder = new google.maps.Geocoder()
        return new Promise(function (resolve, reject) {
          geocoder.geocode({ address: _location }, function (results, status) {
            if (status == google.maps.GeocoderStatus.OK) {
              this.lat = results[0].geometry.location.lat()
              this.lng = results[0].geometry.location.lng()
              this.full_location = results[0].formatted_address
              coords = {
                lat: this.lat,
                lng: this.lng,
                full_location: this.full_location
              }
              resolve(coords)
            } else {
              alert('Oops! Couldn\'t get data for the location')
            }
          })
        })
      },
      setFormatCoordinates: async function () {
        var coordinates = await this.getCoordinates();
        this.lat = coordinates.lat;
        this.lng = coordinates.lng;
        this.currentWeather.full_location = coordinates.full_location;
        // Remember to beautify lat for N/S
        if (coordinates.lat > 0) {
          this.currentWeather.formatted_lat =
            (Math.round(coordinates.lat * 10000) / 10000).toString() + '°N';
        } else if (coordinates.lat < 0) {
          this.currentWeather.formatted_lat =
            (-1 * (Math.round(coordinates.lat * 10000) / 10000)).toString() +
            '°S';
        } else {
          this.currentWeather.formatted_lat = (
            Math.round(coordinates.lat * 10000) / 10000
          ).toString();
        }
        // Remember to beautify longitude for N/S
        if (coordinates.lng > 0) {
          this.currentWeather.formatted_lng =
            (Math.round(coordinates.lng * 10000) / 10000).toString() + '°E';
        } else if (coordinates.lng < 0) {
          this.currentWeather.formatted_lng =
            (-1 * (Math.round(coordinates.lng * 10000) / 10000)).toString() +
            '°W';
        } else {
          this.currentWeather.formatted_lng = (
            Math.round(coordinates.lng * 10000) / 10000
          ).toString();
        }
      },
      fixWeatherApi: async function () {
        await this.setFormatCoordinates();
        var weatherApi =
          'https://csm.fusioncharts.com/files/assets/wb/wb-data.php?src=darksky&lat=' +
          this.lat +
          '&long=' +
          this.lng;
        this.completeWeatherApi = weatherApi;
      },
      fetchWeatherData: async function () {
        await this.fixWeatherApi()
        let weatherApiResponse = await axios.get(this.completeWeatherApi)
        if (weatherApiResponse.status === 200) {
          this.rawWeatherData = weatherApiResponse.data;
          console.log(this.rawWeatherData)
        } else {
          alert('Hmm... Seems like our weather experts are busy!');
        }
      },

      //getters and setters
      getTimezone() {
        return this.rawWeatherData.timezone
      },
      getSetCurrentTime() {
        let currentTime = this.rawWeatherData.currently.time;
        let timezone = this.getTimezone();
        this.currentWeather.time = this.unixToHuman(
          timezone,
          currentTime
        ).fullTime;
      },
      getSetSummary: function () {
        var currentSummary = this.titleCase(
          this.rawWeatherData.currently.summary
        );
        if (currentSummary.includes(' And')) {
          currentSummary = currentSummary.replace(' And', ',');
        }
        this.currentWeather.summary = currentSummary;
      },
      getSetPossibility: function () {
        var possible = this.titleCase(this.rawWeatherData.daily.icon);
        if (possible.includes(' And')) {
          possible = possible.replace(' And', ',');
        }
        this.currentWeather.possibility = possible;
      },
      getSetCurrentTemp: function () {
        var currentTemp = parseInt(this.rawWeatherData.currently.temperature);
        this.currentWeather.temp = currentTemp;
      },
      getTodayDetails: function () {
        return this.rawWeatherData.daily.data[0];
      },
      getSetTodayTempHighLowWithTime: function () {
        var timezone = this.getTimezone();
        var todayDetails = this.getTodayDetails();
        this.currentWeather.todayHighLow.todayTempHigh = parseInt(todayDetails.temperatureMax)

        this.currentWeather.todayHighLow.todayTempHighTime = this.unixToHuman(
          timezone,
          todayDetails.temperatureMaxTime
        ).onlyTime
        this.currentWeather.todayHighLow.todayTempLow = parseInt(todayDetails.temperatureMin)
        this.currentWeather.todayHighLow.todayTempLowTime = this.unixToHuman(
          timezone,
          todayDetails.temperatureMinTime
        ).onlyTime;
      },
      getHourlyInfoToday: function () {
        return this.rawWeatherData.hourly.data;
      },
      getSetHourlyTempInfoToday: function () {
        //debugger;
        var unixTime = this.rawWeatherData.currently.time;
        var timezone = this.getTimezone();
        var todayMonthDate = this.unixToHuman(timezone, unixTime).onlyMonthDate;
        var hourlyData = this.getHourlyInfoToday();

        for (var i = 0; i < hourlyData.length; i++) {
          var hourlyTimeAllTypes = this.unixToHuman(timezone, hourlyData[i].time);
          var hourlyOnlyTime = hourlyTimeAllTypes.onlyTime;
          var hourlyMonthDate = hourlyTimeAllTypes.onlyMonthDate;
          if (todayMonthDate === hourlyMonthDate) {
            var hourlyObject = { hour: '', temp: '' };
            hourlyObject.hour = hourlyOnlyTime;
            hourlyObject.temp = hourlyData[i].temperature
            this.tempVar.tempToday.push(hourlyObject);
            /*
            Since we are using array.push(), we are just adding elements
            at the end of the array. Thus, the array is not getting emptied
            first when a new location is entered.
            to solve this problem, a method this.makeTempVarTodayEmpty()
            has been created, and called from this.locationEntered().
            */
          }
        }
        /*
        To cover the edge case where the local time is between 10 — 12 PM,
        and therefore there are only two elements in the array
        this.tempVar.tempToday. We need to add the points for minimum temperature
        and maximum temperature so that the chart gets generated with atleast four points.
        */
        if (this.tempVar.tempToday.length <= 2) {
          var minTempObject = {
            hour: this.currentWeather.todayHighLow.todayTempHighTime,
            temp: this.currentWeather.todayHighLow.todayTempHigh
          };
          var maxTempObject = {
            hour: this.currentWeather.todayHighLow.todayTempLowTime,
            temp: this.currentWeather.todayHighLow.todayTempLow
          };
          /*
          Typically, lowest temp are at dawn,
          highest temp is around mid day.
          Thus we can safely arrange like min, max, temp after 10 PM.
          */
          // array.unshift() adds stuff at the beginning of the array.
          // the order will be: min, max, 10 PM, 11 PM.
          this.tempVar.tempToday.unshift(maxTempObject, minTempObject);
        }
      },

      // For Today Highlights
      getSetUVIndex: function () {
        var uvIndex = this.rawWeatherData.currently.uvIndex;
        this.highlights.uvIndex = uvIndex;
      },
      getSetVisibility: function () {
        var visibilityInMiles = this.rawWeatherData.currently.visibility;
        this.highlights.visibility = this.mileToKilometer(visibilityInMiles);
      },
      getSetWindStatus: function () {
        var windSpeedInMiles = this.rawWeatherData.currently.windSpeed;
        this.highlights.windStatus.windSpeed = this.mileToKilometer(
          windSpeedInMiles
        );
        var absoluteWindDir = this.rawWeatherData.currently.windBearing;
        this.highlights.windStatus.windDirection = absoluteWindDir;
        this.highlights.windStatus.derivedWindDirection = this.deriveWindDir(
          absoluteWindDir
        );
      },
      organizeCurrentWeatherInfo: function () {
        // data in this.currentWeather
        this.getSetCurrentTime();
        this.getSetCurrentTemp();
        this.getSetTodayTempHighLowWithTime();
        this.getSetSummary();
        this.getSetPossibility();
      },
      organizeTodayHighlights: function () {
        // top level for highlights
        this.getSetUVIndex();
        this.getSetVisibility();
        this.getSetWindStatus();
      },

      // Top level organization and rendering
      organize: async function () {
        // top level organization
        await this.fetchWeatherData();
        this.organizeCurrentWeatherInfo();
        this.organizeTodayHighlights();
        this.getSetHourlyTempInfoToday();
      }
    },
    computed: {
    }
  }
</script>

<style>
  body {
    font-family: 'Avenir', Arial, Helvetica, sans-serif;
    font-weight: 400;
    width: 100%;
    margin: 0;
    font-size: 1.6rem;
  }

  header,
  main,
  footer {
    padding-left: 300px;
  }

  nav {
    background: white !important;
    height: 128px;
    line-height: 64px;
    color: #5474fb !important;
    box-shadow: none;
  }

  .sidenav {
    width: 340px;
  }

  #sidenav-1>div {
    margin-left: 15px;
    display: flex;
  }

  div.logo {
    position: fixed;
    right: -5px;
  }

  div.logo>a>span {
    margin-right: 12px;
  }

  img#logo {
    width: 128px;
    max-width: 128px;
  }

  #btn-go {
    background-color: rgb(84, 116, 251);
    padding: 22px 19px;
    margin-left: 3px;
    margin-right: 6px;
    line-height: 3px;
  }

  #icon_prefix {
    width: calc(85% - 3rem)
  }

  #sidenav-1>div>label {
    color: #5474fb;
  }

  #sidenav-1 > div.row > div > div:nth-child(3) > div > div > h6 {
    font-size: 18px;
}

  @media only screen and (max-width : 992px) {

    header,
    main,
    footer {
      padding-left: 0;
    }

    div>a>span {
      display: none;
    }

  }

  @media only screen and (min-width: 201px) {

    nav,
    nav .nav-wrapper i,
    nav a.sidenav-trigger,
    nav a.sidenav-trigger i {
      height: 128px;
      line-height: 128px;
      color: #5474fb !important;
    }


  }
</style>