<script>
  import { onMount } from 'svelte';
  import axios from 'axios';

  let weatherData = null;

  onMount(async () => {
    try {
      const position = await new Promise((resolve, reject) =>
        navigator.geolocation.getCurrentPosition(resolve, reject)
      );
  
      const { latitude, longitude } = position.coords;
  
      const response = await axios.get(
        `https://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=747d98a119de65b2f0e71e585db307df`
      );
  
      weatherData = response.data;
    } catch (error) {
      console.error("Error:", error);
    }
  });
</script>

<main>
  {#if weatherData}
    <h1>The weather in your location ({weatherData.name}):</h1>
    <p>Temperature: {Math.round(weatherData.main.temp - 273.15)}°C</p>
    <p>Humidity: {weatherData.main.humidity}%</p>
    <p>Weather: {weatherData.weather[0].description}</p>
  {:else}
    <p>Loading weather data...</p>
  {/if}
</main>