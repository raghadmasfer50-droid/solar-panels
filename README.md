# solar-panels
const LAT = 24.1556;   // الخرج
const LON = 47.3346;

const url = `https://api.open-meteo.com/v1/forecast?latitude=${LAT}&longitude=${LON}&current=temperature_2m,wind_speed_10m,shortwave_radiation&timezone=Asia%2FRiyadh`;

// استخدمي this.helpers بدل fetch
const response = await this.helpers.httpRequest({
  method: 'GET',
  url: url,
  json: true
});

const current = response.current;

const radiation = current.shortwave_radiation || 0;
const elevation = radiation > 0 ? Math.min(90, radiation / 10) : 0;

const windSpeed = current.wind_speed_10m;
const stopPanels = windSpeed > 50;

return [{
  json: {
    location: "Al Kharj, Saudi Arabia",
    timestamp: new Date().toISOString(),

    temperature: current.temperature_2m,
    wind_speed: windSpeed,
    solar_radiation: radiation,

    solar_elevation_estimated: elevation.toFixed(2),
    panel_tilt: elevation.toFixed(2),

    panels_status: stopPanels 
      ? "STOPPED ⚠️ (High Wind)" 
      : "RUNNING ✅",

    efficiency_estimate: radiation > 0 
      ? Math.min(100, (radiation / 1000) * 100).toFixed(1)
      : "0"
  }
}];
