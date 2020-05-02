<script>
  import { onMount } from 'svelte'
  
  const API_KEY = '2H8SUN2YauYx3HjApX5DScnJrYEe3fDrwlNyUHwQ'
  const API = `https://api.nasa.gov/insight_weather/?api_key=${API_KEY}&feedtype=json&ver=1.0`

  let info = []
  let currentInfo = {}
  let highTemp = 0.0
  let lowTemp = 0.0
  let wSpeed = 0
  let wDirection
  let wArrow
  let today = new Date().toDateString()

  onMount(async () => {
    const res = await fetch(API)
    const data = await res.json()
    const {
      sol_keys,
      validity_checks,
      ...solData
    } = data
    // console.log(solData);

    // loop through object
    const temp = Object.entries(solData).map(([solData, data]) => {
      return {
        
        maxTemp: data.AT.mx,
        minTemp: data.AT.mn,
        windSpeed: data.HWS.av,
        windDirectionDegrees: data.WD.most_common.compass_degrees,
        windDirectionCardinal: data.WD.most_common.compass_point,
        date: new Date(data.First_UTC)
      }
    })
    info = temp
    currentInfo = info[info.length-1]
    console.log(currentInfo);
    const { date, maxTemp, minTemp, windDirectionaCardinal, windDirectionDegrees, windSpeed } = currentInfo
    // today = date
    highTemp = maxTemp
    lowTemp = minTemp
    wSpeed = windSpeed
    wDirection = windDirectionDegrees
    wArrow = windDirectionaCardinal
  })

</script>

<main class="mars-current-weather">
  <h1 class="main-title">Latest weather at Elysium Planitia</h1>

  <div class="date">
    <h2 class="section-title section-title--date">
      Today
      <!-- <span data-current-sol>Not Available</span> -->
    </h2>
    <p class="date__day">{ today }</p> 
  </div>

  <div class="temp">
    <h2 class="section-title">Temperature</h2>
    <p class="reading">
      High: { highTemp } °C
    </p>
    <p class="reading">
      Low: { lowTemp } °C
    </p>
  </div>

  <div class="wind">
    <h2 class="section-title">Wind</h2>
    <p class="reading">
      <span>{ wSpeed } kph</span>
      <span data-speed-unit />
    </p>

    <div class="wind__direction">
      <p class="sr-only">{ wDirection }</p>
      <div class="wind__arrow">{ wArrow }</div>
    </div>
  </div>

  <div class="info">
    <p>
      InSight is taking daily weather measurements (temperature, wind, pressure)
      on the surface of Mars at Elysium Planitia, a flat, smooth plain near
      Mars’ equator.
    </p>
    <p>
      This is only a part of InSight’s mission.
      <a href="https://mars.nasa.gov/insight/mission/overview/">Click here</a>
      to find out more.
    </p>
  </div>

  <div class="unit">
    <label for="cel">°C</label>
    <input type="radio" id="cel" name="unit" checked />
    <!-- when unit__toggle is clicked checkbox needs to change -->
    <button class="unit__toggle" data-unit-toggle />
    <label for="fah">°F</label>
    <input type="radio" id="fah" name="unit" />
  </div>

</main>

<div class="previous-weather">
  <!-- When clicked, toggle '.show-weather' to .previous-weather div -->
  <button for="weather-toggle" class="show-previous-weather">
    <span>&#8593;</span>
    <span class="sr-only">Show previous weather</span>
  </button>

  <h2 class="main-title previous-weather__title">Previous 7 days</h2>

  <div class="previous-days" data-previous-sols />
</div>

<template data-previous-sol-template>
  <div class="previous-day">
    <h3 class="previous-day__sol">
      Sol
      <span data-sol />
    </h3>
    <p class="previous-day__date" data-date />
    <p class="previous-day__temp">
      High:
      <span data-temp-high />
      °
      <span data-temp-unit />
    </p>
    <p class="previous-day__temp">
      Low:
      <span data-temp-low />
      °
      <span data-temp-unit />
    </p>
    <button class="previous-day__more-info" data-select-button>
      more info
    </button>
  </div>
</template>
