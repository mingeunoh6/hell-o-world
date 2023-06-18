<script>
  import { afterUpdate, onDestroy } from 'svelte';
  import axios from 'axios';
  import Weather from './Weather.svelte';

  let forecastData = null;
  let currentTime = new Date().toLocaleTimeString();
  let currentDate = new Date();
  const API_KEY = '747d98a119de65b2f0e71e585db307df';
  let currentFormattedTime = formatDateTime(currentDate);
  let intervalId, watcherId;

  function formatDateTime(date) {
    return `${date.toLocaleDateString()}, ${date.toLocaleTimeString()}, ${date.toLocaleString('default', { weekday: 'long' })}`;
  }

  async function refreshData(latitude, longitude) {
    try {
      const response = await axios.get(
        `https://api.openweathermap.org/data/2.5/forecast?lat=${latitude}&lon=${longitude}&appid=${API_KEY}`
      );

      forecastData = response.data;
    } catch (error) {
      console.error("Error:", error);
    }
  }

  function handleGeolocationSuccess(position) {
    refreshData(position.coords.latitude, position.coords.longitude);
  }

  function handleGeolocationError(error) {
    console.error("Error fetching position: ", error);
  }

  let isFirstLoad = true;

  afterUpdate(() => {
    if (isFirstLoad) {
      navigator.geolocation.getCurrentPosition(
        handleGeolocationSuccess,
        handleGeolocationError,
        { maximumAge: 0, timeout: 5000, enableHighAccuracy: true }
      );

      watcherId = navigator.geolocation.watchPosition(
        handleGeolocationSuccess,
        handleGeolocationError,
        { maximumAge: 0, timeout: 5000, enableHighAccuracy: true }
      );

      intervalId = setInterval(() => {
        currentDate = new Date();
        currentFormattedTime = formatDateTime(currentDate);
      }, 1000);

      isFirstLoad = false;
    }
  });

  onDestroy(() => {
    clearInterval(intervalId);
    navigator.geolocation.clearWatch(watcherId);
  });
</script>

<h1>Current time: {currentFormattedTime}</h1>
<Weather />

{#if forecastData}
  <h1>Forecast</h1>
  <p>3-hour step forecast data for the next 5 days</p>
{#each forecastData.list as item (item.dt)}
  <div>
    <h2>{new Date(item.dt * 1000).toLocaleString()}</h2>
    <p>{item.weather[0].description}</p>
    <p>Temperature: {Math.round(item.main.temp - 273.15)} °C</p>
  </div>
{/each}
{:else}
  <p>Loading forecast...</p>
{/if}