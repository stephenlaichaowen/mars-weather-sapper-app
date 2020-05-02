<script>
  import { onMount } from "svelte";

  const API_KEY = "2H8SUN2YauYx3HjApX5DScnJrYEe3fDrwlNyUHwQ";
  const API = `https://api.nasa.gov/insight_weather/?api_key=${API_KEY}&feedtype=json&ver=1.0`;

  let info = [];
  let currentInfo = {};
  let highTemp = '0.0';  
  let lowTemp = '0.0';
  let wSpeed = 0;
  let wDirection;
  let wArrow;
  let today 
  let locales = 'en-US'
  let temperatureText = ''
  let highTempText = ''
  let lowTempText = ''
  let todayText = ''
  let windText = ''
  let lang = true;

  $: if (lang) { 
    today = new Date().toLocaleString('en-US')
    todayText = 'Today'
    temperatureText = 'Temperature'
    highTempText = 'High:'
    lowTempText = 'Low:'
    windText = 'Wind'
  }

  $: if (!lang) { 
    today = new Date().toLocaleString('zh-TW')
    todayText = '今天'
    temperatureText = '氣溫'
    highTempText = '高溫'
    lowTempText = '低溫'
    windText = '風'
  }

  onMount(async () => {
    const res = await fetch(API);
    const data = await res.json();
    const { sol_keys, validity_checks, ...solData } = data;
    console.log(solData);

    const temp = Object.entries(solData).map(([solData, data]) => {
      return {
        maxTemp: data.AT.mx,
        minTemp: data.AT.mn,
        windSpeed: data.HWS.av,
        windDirectionDegrees: data.WD.most_common.compass_degrees,
        windDirectionCardinal: data.WD.most_common.compass_point,
        date: new Date(data.First_UTC)
      };
    });

    info = temp;
    currentInfo = info[info.length - 1];    

    const {
      date,
      maxTemp,
      minTemp,
      windDirectionaCardinal,
      windDirectionDegrees,
      windSpeed
    } = currentInfo;

    highTemp = maxTemp;
    lowTemp = minTemp;
    wSpeed = windSpeed;
    wDirection = windDirectionDegrees;
    wArrow = windDirectionaCardinal;
  });

  function switchLang() {
    lang = !lang    
  }
</script>

<main class="mars-current-weather">
  <h1 class="main-title" style="display: flex; justify-content: space-between">
    {#if lang}Latest weather at Elysium Planitia{:else}極樂世界的最新天氣{/if}
    <i class="fas fa-globe" on:click={switchLang} style="cursor: pointer" />
  </h1>

  <div class="date">
    <h2 class="section-title section-title--date">{ todayText }</h2>
    <p class="date__day">{today}</p>
  </div>

  <div class="temp">
    <h2 class="section-title">{ temperatureText }</h2>
    <p class="reading">
      { highTempText }
      {highTemp} °C
    </p>
    <p class="reading">
      { lowTempText }
      {lowTemp} °C
    </p>
  </div>

  <div class="wind">
    <h2 class="section-title">{ windText }</h2>
    <p class="reading">
      <span>{wSpeed} kph</span>
      <span data-speed-unit />
    </p>

    <div class="wind__direction">
      <p class="sr-only">{wDirection}</p>
      <div class="wind__arrow">{wArrow}</div>
    </div>
  </div>

  <div class="info">
    <p>
      {#if lang}
        InSight is taking daily weather measurements (temperature, wind,
        pressure) on the surface of Mars at Elysium Planitia, a flat, smooth
        plain near Mars’ equator.
      {:else}
        InSight 每天在火星表面的極樂世界（位於火星赤道附近的一個平坦而光滑的平原）進行溫度，風，壓力等天氣測量。
      {/if}
    </p>
    <p>
      {#if lang} This is only a part of InSight’s mission. {:else} 這只是 InSight 任務的一部分。 {/if}      
      <a href="https://mars.nasa.gov/insight/mission/overview/">
        {#if lang}Click here{:else}請點擊這裡{/if}
      </a>
      {#if lang}Click here{:else}了解更多{/if}
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

<style>
  main {
    margin-top: 2rem;
  }
</style>