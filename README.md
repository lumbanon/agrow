<p align="center">
  <img src="assets/agrow-logo.png" alt="AGROW" width="260">
</p>

<h1 align="center">AGROW 2.0</h1>

<p align="center">
  A GIS and machine-learning decision-support platform for crop planning in Sabah, Malaysia.
</p>

<p align="center">
  <a href="https://agrow.pages.dev/"><strong>🌐 Try the live demo: agrow.pages.dev</strong></a>
</p>

---

> **Demo availability:** the backend runs on Google Cloud Run using **Google Cloud free-trial credits**, which end on **[DATE]**.
> After that date the website still opens, but sign-in and analyses stop working.
> If the demo is unavailable, please contact me and I can show it live or walk you through it.

## Try it in 3 minutes

1. Open **https://agrow.pages.dev/**.
2. Sign in with the demo account, or register with your own email (a verification link is sent):

   | Email | Password |
   | --- | --- |
   | `[user@email.com]` | `[123456]` |

3. Things to try:
   - **Dashboard:** click a district on the map and see the suitability class for 47 crops. Pick a crop under *Target Crop Layer* to colour the whole of Sabah.
   - **Farm Analysis:** draw a farm boundary on the map. You get a crop-suitability score, a forest-reserve check, the environmental data used, an estimated gross revenue, and a printable report.
   - **District Comparison:** rank crops for any district.
   - **Crop Statistics:** production, planted area and value for 64 crops across Sabah's districts, 2016 to 2024.
   - **Model Performance:** how the model was evaluated, including its limitations.
   - **Field Assessment:** request soil testing, a land survey or IoT crop monitoring.

> **The first request can take 10–30 seconds.** The server scales to zero when nobody is using it, to keep costs at zero. After it wakes up, the site is fast.

## What it does

AGROW helps growers, agronomists and planners judge whether a crop suits a piece of land. For a farm boundary drawn on the map, AGROW:

- **samples the environment inside it** from PostGIS raster layers: elevation, slope, rainfall, temperature and soil pH;
- **checks it against gazetted forest reserves** and shows any overlap on the map;
- **scores crop suitability** with a trained model, plus agronomic rule checks for each crop;
- **estimates gross revenue** from historical district statistics, with low, median and high scenarios.

Every result states where its data came from and how far it can be trusted. Missing data is shown as missing, never as zero.

## Screenshots

<!-- Add 2–4 screenshots here, e.g.
![Dashboard](assets/dashboard.png)
![Farm Analysis](assets/farm-analysis.png)
-->

## Tech stack

| Layer | Technology |
| --- | --- |
| Frontend | React 19, Vite, React Router, Recharts, Leaflet, Google Maps |
| Backend API | Python, FastAPI, Pydantic v2, SQLAlchemy 2 |
| Database | PostgreSQL with PostGIS, including raster data |
| Machine learning | scikit-learn gradient boosting, pandas, NumPy |
| Hosting | Cloudflare Pages (frontend), Google Cloud Run in Docker (API), Neon serverless PostgreSQL (database) |
| Other | Gmail SMTP for account emails, Google Secret Manager, GitHub Actions CI |

## Engineering highlights

- **Full-stack GIS:** spatial queries on raster and vector data (clipping rasters to a drawn polygon, geodesic area, reserve-overlap detection) run in PostGIS and are exposed through a typed API.
- **Machine-learning pipeline:**
  - features are computed from the same raster layers in training and in production;
  - validation is spatial, holding out whole districts;
  - model files are hash-verified before the API serves them;
  - a model card is published in the app.
- **Security:**
  - email verification and password reset;
  - server-side sessions that can be revoked, including "sign out everywhere";
  - rate limiting, security headers and secrets held in a secret manager;
  - data export and account deletion.
- **Trustworthy records:** saved analyses are created by the server and protected with a SHA-256 integrity hash, so a browser cannot alter a stored result.
- **Production practice:**
  - tracked, checksummed database migrations;
  - health and readiness checks;
  - about 220 automated tests (backend and frontend);
  - CI that runs the backend tests against a real PostGIS database;
  - deployments that can be rolled back.

## Honest limitations

- On 7 held-out districts, the model agrees with AGROW's rule-generated labels 98% of the time. **That measures agreement with the rules, not real-world accuracy.** Checked against where crops are actually produced, its performance is near chance (ROC AUC 0.52). This is documented in the app, not hidden.
- District-level results are screening estimates from map layers. They are not a farm survey.
- The forest-reserve check is not a land-title, zoning or permit check.
- IoT crop monitoring is at an early stage: requests and data storage exist, but no sensors are installed.
