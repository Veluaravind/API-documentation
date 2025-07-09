# 🌦️ OpenWeatherMap API Documentation

## 📖 Overview of the API

The OpenWeatherMap API provides access to **current weather data** for any location worldwide. It delivers weather details such as:

- Temperature
- Atmospheric pressure
- Humidity
- Wind speed and direction
- General weather conditions

Common use cases include web and mobile apps, smart devices, and dashboards for displaying real-time weather information.

---

## 🌍 Endpoint Description

### Get Current Weather Data

- **URL**: `https://api.openweathermap.org/data/2.5/weather`
- **Method**: `GET`
- **Description**: Returns current weather conditions for a specified city.

---

## 🔐 Authentication

To access this API, an API key (`appid`) is **required** in every request.

- **Where to get it**: Sign up at [OpenWeatherMap](https://openweathermap.org/) to obtain a free API key.
- **How to use it**: Include it as a query parameter in the request URL.
  - Example: `appid=YOUR_API_KEY`
- **Security tip**: Never share your API key publicly.

---

## 🧾 Request Parameters

### Required

| Parameter | Type   | Description |
|-----------|--------|-------------|
| `q`       | string | City name. Optionally include the country code (e.g., `q=London,GB`). |
| `appid`   | string | Your unique API key. |

### Optional

| Parameter | Type   | Description |
|-----------|--------|-------------|
| `units`   | string | Units of measurement: `standard` (Kelvin, default), `metric` (Celsius), or `imperial` (Fahrenheit). |
| `lang`    | string | Response language (e.g., `en`, `es`, `fr`, etc.). |
| `lat`     | float  | Latitude coordinate (alternative to `q`). |
| `lon`     | float  | Longitude coordinate (alternative to `q`). |

---

## 🧾 Header

No custom headers are required. Standard HTTPS GET requests using query parameters are sufficient.

---

## 💻 Code Examples

### Python (using `requests`)

```python
import requests

url = "https://api.openweathermap.org/data/2.5/weather"
params = {
    "q": "London",
    "appid": "YOUR_API_KEY",
    "units": "metric"
}
response = requests.get(url, params=params)
print(response.json())
```

### Curl

```bash
curl "https://api.openweathermap.org/data/2.5/weather?q=London&appid=YOUR_API_KEY&units=metric"
```

---

## 📥 Example Request

```http
GET https://api.openweathermap.org/data/2.5/weather?q=London&appid=8aaa7de57751874e8f1c1baa3575c1f6
```

---

## 📤 Example Response (truncated)

```json
{
  "coord": { "lon": -0.13, "lat": 51.51 },
  "weather": [
    { "id": 800, "main": "Clear", "description": "clear sky", "icon": "01d" }
  ],
  "main": {
    "temp": 18.5,
    "pressure": 1016,
    "humidity": 56
  },
  "wind": { "speed": 4.6, "deg": 90 },
  "name": "London",
  "cod": 200
}
```

---

## 🧾 Response Fields

| Field      | Type    | Description                             |
|------------|---------|-----------------------------------------|
| `coord`    | object  | Geographic coordinates (lat, lon).      |
| `weather`  | array   | Weather condition summary.              |
| `main`     | object  | Temperature, pressure, and humidity.    |
| `wind`     | object  | Wind speed and direction.               |
| `clouds`   | object  | Cloudiness percentage.                  |
| `sys`      | object  | Country, sunrise, and sunset times.     |
| `name`     | string  | City name.                              |
| `cod`      | integer | Response status code (e.g., 200 = OK).  |

---

## ❌ Error Handling

| Code | Meaning           | Description                                 |
|------|-------------------|---------------------------------------------|
| 200  | OK                | Successful API call.                        |
| 401  | Unauthorized      | Invalid or missing API key.                 |
| 404  | Not Found         | City not found or invalid request.          |
| 429  | Too Many Requests | API rate limit exceeded.                    |
| 500  | Internal Error    | Server-side error.                          |

---

## ⚠️ Notes

- Temperatures are returned in **Kelvin** by default.
- Use `units=metric` or `imperial` for user-friendly formats.
- Always validate the `cod` field to confirm request success.
- Do not expose your API key in frontend code or public repositories.

---

## 💡 FAQs and Best Practices

### Frequently Asked Questions

- **Q: How do I get temperatures in Fahrenheit?**  
  A: Add `units=imperial` to your request.

- **Q: Can I get weather data using coordinates?**  
  A: Yes, use `lat` and `lon` instead of `q`.

### Best Practices

- **Cache responses** where possible to reduce API calls.
- **Monitor rate limits** to avoid disruptions (especially if using the free tier).
- **Specify language (`lang`)** for better localization.
- **Use HTTPS** to ensure secure communication.

---
