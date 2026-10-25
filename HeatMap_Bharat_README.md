# HeatMap Bharat

**Satellite-Based Heatwave Intelligence and Early Warning System**  
**Team:** Team Reformers · **HSC Team ID:** HSCTN01377 · **Pilot city:** Chennai, Tamil Nadu, India

HeatMap Bharat is an open-source GIS dashboard prototype for visualising relative neighbourhood heat patterns using satellite-derived land-surface temperature (LST), vegetation indicators, built-up intensity proxies, and weather observations.

> **Prototype status:** The current implementation uses synthetic sample data to demonstrate the dashboard and API. It does not provide verified live Chennai measurements, a validated public-health model, or an operational heatwave-warning service.

## Features

- Interactive React dashboard with a Leaflet map.
- FastAPI backend exposing sample hotspot, weather, city, and layer endpoints.
- Transparent, configurable relative heat score.
- Synthetic sample data for local development and automated tests.
- Test cases for scoring, API responses, invalid inputs, and missing records.
- Architecture documentation and data dictionary.
- MIT open-source license.

## Repository structure

```text
heatmap-bharat/
├── backend/
│   ├── app/
│   │   ├── main.py
│   │   ├── data.py
│   │   └── scoring.py
│   ├── tests/
│   │   ├── test_api.py
│   │   └── test_scoring.py
│   ├── requirements.txt
│   └── .env.example
├── frontend/
│   ├── src/
│   │   ├── App.jsx
│   │   ├── main.jsx
│   │   └── style.css
│   ├── index.html
│   ├── package.json
│   └── .env.example
├── data/
│   └── sample_neighbourhoods.json
├── docs/
│   ├── ARCHITECTURE.md
│   └── DATA_DICTIONARY.md
├── .gitignore
├── LICENSE
└── README.md
```

## Requirements

- Python 3.10 or newer
- Node.js 20 or newer and npm
- Internet access for package installation and OpenStreetMap map tiles

## Quick start

### 1. Clone the repository

After creating the repository on GitHub or GitLab, replace the placeholder URL with your actual repository URL:

```bash
git clone https://github.com/<YOUR_USERNAME>/heatmap-bharat.git
cd heatmap-bharat
```

### 2. Start the backend API

From the project root, run the commands for your operating system.

**Windows PowerShell**

```powershell
cd backend
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
Copy-Item .env.example .env
uvicorn app.main:app --reload
```

**macOS / Linux**

```bash
cd backend
python3 -m venv .venv
source .venv/bin/activate
python -m pip install --upgrade pip
pip install -r requirements.txt
cp .env.example .env
uvicorn app.main:app --reload
```

The API will be available at:

- API base: <http://127.0.0.1:8000>
- Interactive API documentation: <http://127.0.0.1:8000/docs>
- Health check: <http://127.0.0.1:8000/health>

Keep this terminal open while using the dashboard.

### 3. Run backend tests

Open a second terminal:

```bash
cd heatmap-bharat/backend
```

Activate the virtual environment, then run:

```bash
pytest -q
```

Expected result: all tests pass. If dependencies are missing, install them using `pip install -r requirements.txt` from the `backend` directory.

### 4. Start the frontend

Open a third terminal:

```bash
cd heatmap-bharat/frontend
npm install
```

Create the local environment file:

**Windows PowerShell**
```powershell
Copy-Item .env.example .env
```

**macOS / Linux**
```bash
cp .env.example .env
```

Start the Vite development server:

```bash
npm run dev
```

Open the local URL shown in the terminal, usually <http://localhost:5173>.

The backend and frontend must both be running. The map uses OpenStreetMap tiles and therefore needs an internet connection.

## Environment variables

Environment files are local configuration and should not be committed. The `.gitignore` excludes `.env` files.

### Backend: `backend/.env`

| Variable | Default | Description |
|---|---|---|
| `APP_NAME` | `HeatMap Bharat API` | API title displayed in API documentation |
| `DEMO_MODE` | `true` | Marks the service as using demo data |
| `CORS_ORIGINS` | `http://localhost:5173,http://127.0.0.1:5173` | Comma-separated allowed frontend origins |

Example:

```env
APP_NAME=HeatMap Bharat API
DEMO_MODE=true
CORS_ORIGINS=http://localhost:5173,http://127.0.0.1:5173
```

### Frontend: `frontend/.env`

| Variable | Default | Description |
|---|---|---|
| `VITE_API_BASE_URL` | `http://127.0.0.1:8000` | Base URL for the FastAPI backend |

Example:

```env
VITE_API_BASE_URL=http://127.0.0.1:8000
```

No API keys are required to run the bundled demo. If a future data provider requires credentials, store them in environment variables and never commit secrets to version control.

## API endpoints

| Method | Endpoint | Description |
|---|---|---|
| `GET` | `/health` | API health and demo status |
| `GET` | `/api/v1/cities` | List supported cities |
| `GET` | `/api/v1/layers?city=chennai` | List map layers and their data status |
| `GET` | `/api/v1/weather?city=chennai` | Return sample weather context |
| `GET` | `/api/v1/hotspots?city=chennai` | Return neighbourhoods ranked by prototype score |
| `GET` | `/api/v1/neighbourhoods/{id}` | Retrieve one neighbourhood |

Unsupported cities and unknown neighbourhood IDs return HTTP `404`.

## Relative heat-risk score

The prototype uses the following illustrative formula:

\[
R = 0.50T + 0.30B + 0.20(1 - V)
\]

Where:

- `R` = relative score between 0 and 1
- `T` = normalized surface-temperature indicator, between 0 and 1
- `B` = normalized built-up intensity indicator, between 0 and 1
- `V` = normalized vegetation indicator, between 0 and 1

The weights are provisional and are not scientifically validated. The score is intended only to demonstrate a transparent ranking workflow. It is **not** a validated heatwave forecast, medical risk assessment, or official warning.

## Sample data and scientific limitations

The file `data/sample_neighbourhoods.json` and the API's bundled data contain **synthetic demonstration values**. They are not actual measurements of Chennai's neighbourhoods and must not be reported as real observations.

Important distinctions:

- **Land-surface temperature (LST)** is a surface temperature retrieved or derived from thermal remote-sensing data. It is not the same as near-surface air temperature.
- **Sentinel-2** can provide vegetation indicators such as NDVI, but it is not a thermal land-surface-temperature sensor.
- **ECOSTRESS and Landsat** thermal data availability depends on acquisition dates, coverage, cloud conditions, product selection, and quality masks.
- **Meteor-M** imagery may be explored as an optional satellite-reception module, but it is not the primary source of land-surface temperature.
- Different sources must be aligned carefully in space and time before combining their indicators.

## Planned real-data integration

1. Define the Chennai area of interest (AOI) and coordinate reference system.
2. Acquire a suitable ECOSTRESS or Landsat land-surface-temperature product.
3. Apply quality masks and record product IDs, units, timestamps, and missing-data coverage.
4. Derive vegetation indicators from Sentinel-2 or another appropriate source.
5. Select and document a built-up or impervious-surface proxy.
6. Reproject, clip, and align spatial grids and dates.
7. Normalize indicators using reproducible documented bounds.
8. Validate and calibrate score weights against independent observations before any operational use.
9. Add persistent geospatial storage and raster-serving components when the data pipeline is ready.
10. Add live weather and optional Meteor-M reception only after each data source is independently tested.

## Testing

Automated backend tests cover:

- Score outputs at minimum and maximum valid inputs.
- Formula calculation.
- Rejection of values outside the normalized range or non-finite values.
- API health response.
- Hotspot ranking order and synthetic-data labelling.
- Unsupported cities and unknown neighbourhood IDs.

Run them from the `backend` directory:

```bash
pytest -q
```

## Architecture and technical documentation

- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) — system components, architecture diagram, request flow, pipeline, and design constraints.
- [`docs/DATA_DICTIONARY.md`](docs/DATA_DICTIONARY.md) — fields, units, meanings, and sample-data status.

## Data sources and references

These are planned or relevant source documentation links; the current demo does not automatically ingest them.

- NASA ECOSTRESS: <https://ecostress.jpl.nasa.gov/>
- USGS Landsat Missions: <https://www.usgs.gov/landsat-missions>
- Copernicus Data Space: <https://dataspace.copernicus.eu/>
- Open-Meteo API documentation: <https://open-meteo.com/en/docs>
- FastAPI: <https://fastapi.tiangolo.com/>
- Leaflet: <https://leafletjs.com/>
- Rasterio: <https://rasterio.readthedocs.io/>
- PostGIS: <https://postgis.net/documentation/>

## Team

- **Team name:** Team Reformers
- **HSC Team ID:** HSCTN01377
- **Project:** HeatMap Bharat

Add actual team member names, contribution details, and the final public repository URL before submission.

## License

This project is released under the MIT License. See [`LICENSE`](LICENSE).
