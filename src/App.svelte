<script>
  import { onMount } from 'svelte';

  let weather = null;
  let loading = true;
  let error = null;
  let locationName = '';

  // Using Open-Meteo API (free, no API key required)
  async function getWeather(latitude, longitude) {
    try {
      // Get weather data
      const weatherResponse = await fetch(
        `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current=temperature_2m,relative_humidity_2m,apparent_temperature,precipitation,weather_code,wind_speed_10m&temperature_unit=fahrenheit&wind_speed_unit=mph`
      );
      const weatherData = await weatherResponse.json();

      // Get location name using reverse geocoding
      const geoResponse = await fetch(
        `https://geocoding-api.open-meteo.com/v1/reverse?latitude=${latitude}&longitude=${longitude}`
      );
      const geoData = await geoResponse.json();
      
      if (geoData.results && geoData.results.length > 0) {
        const location = geoData.results[0];
        locationName = `${location.name || ''}${location.admin1 ? ', ' + location.admin1 : ''}${location.country ? ', ' + location.country : ''}`;
      } else {
        locationName = `${latitude.toFixed(2)}, ${longitude.toFixed(2)}`;
      }

      weather = weatherData.current;
      loading = false;
    } catch (err) {
      error = 'Failed to fetch weather data: ' + err.message;
      loading = false;
    }
  }

  function getWeatherDescription(code) {
    const weatherCodes = {
      0: '☀️ Clear sky',
      1: '🌤️ Mainly clear',
      2: '⛅ Partly cloudy',
      3: '☁️ Overcast',
      45: '🌫️ Foggy',
      48: '🌫️ Foggy',
      51: '🌦️ Light drizzle',
      53: '🌦️ Moderate drizzle',
      55: '🌧️ Dense drizzle',
      61: '🌧️ Slight rain',
      63: '🌧️ Moderate rain',
      65: '🌧️ Heavy rain',
      71: '🌨️ Slight snow',
      73: '🌨️ Moderate snow',
      75: '🌨️ Heavy snow',
      77: '❄️ Snow grains',
      80: '🌦️ Slight rain showers',
      81: '🌧️ Moderate rain showers',
      82: '⛈️ Violent rain showers',
      85: '🌨️ Slight snow showers',
      86: '🌨️ Heavy snow showers',
      95: '⛈️ Thunderstorm',
      96: '⛈️ Thunderstorm with hail',
      99: '⛈️ Thunderstorm with heavy hail'
    };
    return weatherCodes[code] || '🌡️ Unknown';
  }

  onMount(() => {
    if ('geolocation' in navigator) {
      navigator.geolocation.getCurrentPosition(
        (position) => {
          getWeather(position.coords.latitude, position.coords.longitude);
        },
        (err) => {
          error = 'Failed to get location: ' + err.message;
          loading = false;
        }
      );
    } else {
      error = 'Geolocation is not supported by your browser';
      loading = false;
    }
  });
</script>

<main>
  <div class="container">
    <h1>🌤️ Weather App</h1>
    
    {#if loading}
      <div class="loading">
        <div class="spinner"></div>
        <p>Getting your location and weather...</p>
      </div>
    {:else if error}
      <div class="error">
        <p>❌ {error}</p>
      </div>
    {:else if weather}
      <div class="weather-card">
        <h2>{locationName}</h2>
        <div class="weather-icon">
          {getWeatherDescription(weather.weather_code)}
        </div>
        <div class="temperature">
          <span class="temp-value">{Math.round(weather.temperature_2m)}°F</span>
          <span class="feels-like">Feels like {Math.round(weather.apparent_temperature)}°F</span>
        </div>
        <div class="details">
          <div class="detail-item">
            <span class="label">💧 Humidity</span>
            <span class="value">{weather.relative_humidity_2m}%</span>
          </div>
          <div class="detail-item">
            <span class="label">💨 Wind Speed</span>
            <span class="value">{Math.round(weather.wind_speed_10m)} mph</span>
          </div>
          <div class="detail-item">
            <span class="label">🌧️ Precipitation</span>
            <span class="value">{weather.precipitation} mm</span>
          </div>
        </div>
      </div>
    {/if}
  </div>
</main>

<style>
  :global(body) {
    margin: 0;
    padding: 0;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  }

  main {
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 20px;
  }

  .container {
    text-align: center;
    max-width: 500px;
    width: 100%;
  }

  h1 {
    color: white;
    font-size: 2.5rem;
    margin-bottom: 2rem;
    text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.2);
  }

  .loading {
    background: white;
    padding: 3rem;
    border-radius: 20px;
    box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
  }

  .spinner {
    border: 4px solid #f3f3f3;
    border-top: 4px solid #667eea;
    border-radius: 50%;
    width: 50px;
    height: 50px;
    animation: spin 1s linear infinite;
    margin: 0 auto 1rem;
  }

  @keyframes spin {
    0% { transform: rotate(0deg); }
    100% { transform: rotate(360deg); }
  }

  .loading p {
    color: #666;
    margin: 0;
  }

  .error {
    background: white;
    padding: 2rem;
    border-radius: 20px;
    box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
  }

  .error p {
    color: #e74c3c;
    margin: 0;
    font-size: 1.1rem;
  }

  .weather-card {
    background: white;
    padding: 2.5rem;
    border-radius: 20px;
    box-shadow: 0 10px 40px rgba(0, 0, 0, 0.2);
  }

  .weather-card h2 {
    color: #333;
    font-size: 1.8rem;
    margin: 0 0 1rem 0;
  }

  .weather-icon {
    font-size: 4rem;
    margin: 1rem 0;
  }

  .temperature {
    margin: 1.5rem 0;
  }

  .temp-value {
    display: block;
    font-size: 4rem;
    font-weight: bold;
    color: #667eea;
    line-height: 1;
  }

  .feels-like {
    display: block;
    color: #999;
    font-size: 1rem;
    margin-top: 0.5rem;
  }

  .details {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1rem;
    margin-top: 2rem;
    padding-top: 2rem;
    border-top: 1px solid #eee;
  }

  .detail-item {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .label {
    font-size: 0.9rem;
    color: #999;
  }

  .value {
    font-size: 1.2rem;
    font-weight: bold;
    color: #333;
  }

  @media (max-width: 600px) {
    h1 {
      font-size: 2rem;
    }

    .details {
      grid-template-columns: 1fr;
    }
  }
