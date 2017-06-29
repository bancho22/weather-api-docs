## Weather API

We only expose version 2 of our API to the world.

### GET /api/v2/forecast/{lat},{lon}

Request headers:
```
[x-access-token]: <token, 'distributed via the api for management, see below'>
```

Get the weather forecast for the specified coordinates for a week ahead. Forecast data is grouped in three different fields:
- currently: Contains data for the moment of time when the request was sent.
- hourly: Contains 168 hours worth of data, starting from the next hour after the request was made.
- daily: Contains sun movement data for all 24-hour cycles in which any of the hours included in 'hourly' are part of.

See response body for detailed information.

We have three domain models in our Weather API, listed below in descending order in terms of forecast resolution and weather data included:

- frigg: Our own forecast model, covering Denmark, south of Scandinavia and Northern Europe (usually available for 72 hours ahead)
- conwx: Covering most of Europe
- gfs: Global coverage

We always respond with the best possible data.


Response body:
```
{
    "location": {
        "latitude": <number>,
        "longitude": <number>
    },
    "timezone": <string, timezone_id e.g. 'Europe/Copenhagen', possible values include: 'undefined', 'uninhabited'>,
    "currently": {
        "temperature": <number, celsius>,
        "apparentTemperature": <number, celsius, only available in frigg>,
        "surfaceTemperature": <number, celsius, only available in frigg, not available over water masses>,
        "soilTemperature": <number, celsius, only available in frigg, not available over water masses>,
        "fog": <number, celsius>,
        "windSpeed": <number, m/s>,
        "windBearing": <number, degrees, min: 0, max: 360>,
        "precipIntensity": <number, kg/m**2>,
        "precipIntensityMin": <number, kg/m**2,  only available in frigg>,
        "precipIntensityMax": <number, kg/m**2,  only available in frigg>,
        "precipProbability": <number, percentage, min: 0, max: 1, only available in frigg>,
        "snowIntensity": <number, kg/m**2>,
        "windGust": <number, m/s, not available in conwx>,
        "cloudCover": <number, percentage, min: 0, max: 1>,
        "humidity": <number, percentage, min: 0, max: 1>,
        "pressure": <number, hPa>,
        "dewPoint": <number, celsius, only available in frigg>,
        "time": <timestamp, unix seconds>,
        "model": <string, enum: 'frigg', 'conwx', 'gfs'>,
        "dayLight": <boolean>,
        "icon": <string, enum: cloud-snow, cloud-lightning, heavy-rain, cloud-rain, fog, haze, cloud, sun, moon, sun-cloud, moon-cloud>
    },
    "hourly": [
        {
            "temperature": <number, celsius>,
            "apparentTemperature": <number, celsius, only available in frigg>,
            "surfaceTemperature": <number, celsius, only available in frigg, not available over water masses>,
            "soilTemperature": <number, celsius, only available in frigg, not available over water masses>,
            "fog": <number, celsius>,
            "windSpeed": <number, m/s>,
            "windBearing": <number, degrees, min: 0, max: 360>,
            "precipIntensity": <number, kg/m**2>,
            "precipIntensityMin": <number, kg/m**2,  only available in frigg>,
            "precipIntensityMax": <number, kg/m**2,  only available in frigg>,
            "precipProbability": <number, percentage, min: 0, max: 1, only available in frigg>,
            "snowIntensity": <number, kg/m**2>,
            "windGust": <number, m/s, not available in conwx>,
            "cloudCover": <number, percentage, min: 0, max: 1>,
            "humidity": <number, percentage, min: 0, max: 1>,
            "pressure": <number, hPa>,
            "dewPoint": <number, celsius, only available in frigg>,
            "time": <timestamp, unix seconds>,
            "model": <string, enum: 'frigg', 'conwx', 'gfs'>,
            "dayLight": <boolean>,
            "icon": <string, enum: cloud-snow, cloud-lightning, heavy-rain, cloud-rain, fog, haze, cloud, sun, moon, sun-cloud, moon-cloud>
        },
        ... 167 more items
    ],
    "daily": [
        {
            "time": <timestamp, unix seconds, corresponds to midnight at requested location, defauts to UTC if no timezone is available>,
            "sunriseTime": <timestamp, unix seconds>,
            "sunsetTime": <timestamp, unix seconds>
        },
        ... 7 more items
    ],
    "lastUpdated": <timestamp, unix seconds, the last time the data for the top model you see was updated>
}
```
