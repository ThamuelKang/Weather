# Weather Electron App

An Electron desktop application built with Svelte that displays the current weather for your location.

## Features

- 🌍 Automatic location detection using browser geolocation API
- 🌤️ Real-time weather data from Open-Meteo API (free, no API key required)
- 🎨 Beautiful, modern UI with gradient background
- 📊 Displays temperature, humidity, wind speed, and precipitation
- 🌡️ Shows "feels like" temperature
- 📱 Responsive design

## Prerequisites

- Node.js (v16 or higher)
- npm or yarn

## Installation

1. Install dependencies:
```bash
npm install
```

## Running the App

### Development Mode

To run the app in development mode with hot reload:

```bash
npm run electron:dev
```

This will start both the Vite dev server and Electron. The app will automatically reload when you make changes to the code.

### Production Build

To build the app for production:

```bash
npm run build
npm run electron
```

## Project Structure

```
weather-electron-app/
├── electron/
│   └── main.js          # Electron main process
├── src/
│   ├── App.svelte       # Main Svelte component
│   └── main.js          # Svelte entry point
├── index.html           # HTML entry point
├── package.json         # Project dependencies
├── vite.config.js       # Vite configuration
└── README.md            # This file
```

## Technologies Used

- **Electron** - Desktop application framework
- **Svelte** - Reactive UI framework
- **Vite** - Build tool and dev server
- **Open-Meteo API** - Free weather data API

## How It Works

1. The app requests permission to access your location
2. Once granted, it fetches your coordinates using the browser's Geolocation API
3. The coordinates are sent to Open-Meteo API to get current weather data
4. The location name is retrieved using reverse geocoding
5. All data is displayed in a beautiful, easy-to-read interface

## License

MIT
