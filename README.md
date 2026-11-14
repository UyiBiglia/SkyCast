# SkyCast — Intelligent AI Weather Assistant

[![IBM watsonx Assistant](https://img.shields.io/badge/IBM_watsonx-Assistant-052FAD?style=for-the-badge&logo=ibm&logoColor=white)](https://cloud.ibm.com/products/watsonx-assistant)
[![Open-Meteo REST API](https://img.shields.io/badge/Open--Meteo-REST_API-0284C7?style=for-the-badge&logo=cloud&logoColor=white)](https://open-meteo.com/)
[![GitHub Pages](https://img.shields.io/badge/Live_Demo-GitHub_Pages-22C55E?style=for-the-badge&logo=github&logoColor=white)](https://uyibiglia.github.io/SkyCast/)
[![Portfolio](https://img.shields.io/badge/Portfolio-Project_3-8B5CF6?style=for-the-badge)](https://github.com/UyiBiglia)

> An intelligent, conversational weather assistant built on **IBM watsonx Assistant** and powered by live external **REST APIs (Open-Meteo)**. SkyCast delivers real-time worldwide weather forecasts, rain and umbrella alerts, and temperature-calibrated smart outfit recommendations through natural language.

---

## Live Demonstration

* **Interactive Web App**: [https://uyibiglia.github.io/SkyCast/](https://uyibiglia.github.io/SkyCast/)
* **Assistant Platform**: IBM watsonx Assistant (EU-GB Region)
* **Custom Web Chat Integration**: Embedded responsive chat widget with custom 100x100 branding avatar

---

## Architectural Overview

SkyCast demonstrates how enterprise conversational AI assistants consume external web services without hardcoded data. Using custom **OpenAPI 3.0 extension specifications**, the assistant orchestrates a two-stage API pipeline in real time:

```text
[ User Prompt ] (e.g. "What's the weather in Lagos?")
       │
       ▼
[ IBM watsonx Assistant Action ]
       │
       ▼  Step 1: Geocoding Request
[ Open-Meteo Geocoding REST API ] ──► Returns: latitude, longitude, timezone
       │
       ▼  Step 2: Weather Forecast Request
[ Open-Meteo Forecast REST API ]  ──► Returns: temperature, weathercode, wind speed, precipitation
       │
       ▼
[ Business Logic & Tier Calibrations ] (Temp evaluation, umbrella verdict)
       │
       ▼
[ Natural Conversational Response ] (e.g. "Currently 29°C in Lagos. Breathable clothing recommended.")
```

### 1. Stage 1: Geocoding Resolution
* **Endpoint**: `https://geocoding-api.open-meteo.com/v1/search`
* **Query Parameters**: `name={city}&count=1&language=en&format=json`
* **Response Mapping**:
  * Latitude: `body.results[0].latitude`
  * Longitude: `body.results[0].longitude`
  * Timezone: Dynamic fallback or `auto`

### 2. Stage 2: Meteorological Forecast
* **Endpoint**: `https://api.open-meteo.com/v1/forecast`
* **Query Parameters**: `latitude={lat}&longitude={lon}&current_weather=true&hourly=precipitation_probability&timezone=auto`
* **Response Mapping**:
  * Temperature (°C): `body.current_weather.temperature`
  * Wind Speed (km/h): `body.current_weather.windspeed`
  * WMO Weather Code: `body.current_weather.weathercode`

---

## Core Conversational Actions & Business Logic

### 1. General Weather Check
* **Intent**: Retrieve atmospheric conditions for any city worldwide.
* **Capabilities**: Tested across diverse global locations (e.g., Lagos, Abuja, Benin City, Ekpoma, London, New York).
* **Flow**: Extracts the target city from user input, resolves geographical coordinates, queries current weather metrics, and generates a formatted status card in conversation.

### 2. Rain & Umbrella Decision Engine
* **Intent**: Provide a definitive yes/no recommendation on carrying rain protection.
* **Logic**: Evaluates real-time precipitation probability and WMO meteorological codes (e.g., drizzle, rain showers, thunderstorms).
* **Outputs**:
  * **Rain Expected**: Clear alert advising the user to carry an umbrella or rain gear.
  * **Dry Conditions**: Reassuring notice that skies are clear with no umbrella needed.

### 3. Smart Outfit Advice
* **Intent**: Provide functional clothing recommendations tailored to current local conditions.
* **Calibration Tiers**:
  * **Cool / Cold (< 15°C)**: Warm layers, coat, thermal protection, or heavy knitwear.
  * **Mild / Comfortable (15°C – 24°C)**: Light jacket, cardigan, or comfortable long-sleeve casual wear.
  * **Warm / Hot (> 24°C)**: Lightweight, breathable fabrics (cotton/linen), sun protection, and hydration reminders.

### 4. Interactive Conversational Looping
* Rather than abruptly terminating conversations after a single response, SkyCast presents a decision prompt:
  * *"Would you like to check another city or weather detail?"*
  * Selecting **Yes** seamlessly repeats the inquiry flow while preserving session context.
  * Selecting **No** politely closes the interaction with a friendly sign-off.

### 5. Defensive Error & Fallback Handling
* **Undefined Result Check**: Validates whether `body.results` contains valid coordinate data.
* If a user enters an unrecognized place name or spelling error, SkyCast intercepts the error gracefully before calling the forecast extension:
  * *"I couldn't find coordinates for that location. Please double-check the city name and try again."*

---

## OpenAPI 3.0 Extension Specifications

SkyCast interfaces with Open-Meteo using two validated OpenAPI 3.0 documents configured within the watsonx Assistant Draft environment:

1. **`skycast-geocoding-openapi.json`**:
   * Defines search operation schema, query parameters, and structured JSON output for geographic coordinates.
2. **`skycast-forecast-openapi.json`**:
   * Defines current and hourly forecast schemas, WMO weather codes, and unit conventions.
3. **Authentication**:
   * Configured as public REST endpoints (`no_auth`), eliminating API key expiration risks and rate-limit friction.

---

## Repository Structure

```text
SkyCast/
├── index.html         # Modern, responsive weather showcase landing page
├── README.md          # Comprehensive technical and architectural documentation
└── screenshots/       # Visual proof-of-work documentation (watsonx actions & UI)
    ├── action_general_weather.png
    ├── action_rain_umbrella.png
    ├── action_smart_outfit.png
    └── live_chat_test.png
```

---

## Local Development & Setup

### Prerequisites
* Any modern web browser (Chrome, Edge, Firefox, Safari).
* No Node.js runtime, build step, or external package managers required.

### Running Locally
1. Clone this repository:
   ```bash
   git clone https://github.com/UyiBiglia/SkyCast.git
   cd SkyCast
   ```
2. Open `index.html` directly in your browser:
   * **macOS**: `open index.html`
   * **Windows**: `start index.html`
   * **Linux**: `xdg-open index.html`
3. Click the weather launcher widget in the bottom-right corner to begin chatting with SkyCast in real time.

---

## Deployment to GitHub Pages

To make the SkyCast showcase live on your custom GitHub Pages URL:

1. Create a public repository named `SkyCast` on GitHub.
2. Upload `index.html` and `README.md` to the root of the repository.
3. In GitHub, navigate to **Settings → Pages**.
4. Under **Build and deployment**:
   * **Source**: `Deploy from a branch`
   * **Branch**: `main`
   * **Folder**: `/ (root)`
5. Click **Save**. Your site will be live at `https://uyibiglia.github.io/SkyCast/` within 1–2 minutes.

---

## Technology Stack

* **Conversational AI Platform**: IBM watsonx Assistant
* **External APIs**: Open-Meteo Geocoding API & Weather Forecast API
* **Integration Protocol**: OpenAPI 3.0 Specifications (RESTful GET requests)
* **Frontend Showcase**: HTML5, Modern CSS3 (CSS Variables, Flexbox, CSS Grid)
* **Web Chat Framework**: IBM watsonx Assistant Web Chat v2
* **Hosting**: GitHub Pages

---

## Author & Portfolio Context

* **Developer**: **Uyienminoseye Okodugha**
* **Portfolio**: Part of a three-project IBM watsonx enterprise assistant portfolio:
  1. **Florence Flowers**: E-commerce customer service assistant with product catalog and order inquiries.
  2. **U.O.U.N Virtual Assistant**: University student & admissions assistant with faculty routing and multi-tier fallbacks.
  3. **SkyCast Weather Bot**: Live API-integrated meteorological assistant with dynamic decision logic.
