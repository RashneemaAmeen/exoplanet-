# 🪐 Exoplanet Explorer

A two-part project for exploring planets outside our solar system:

1. **Web scraper (Python):** collects exoplanet data from [NASA's Exoplanet Catalog](https://exoplanets.nasa.gov/exoplanet-catalog/) and saves it as CSV files.
2. **Mobile app (React Native / Expo):** "Planets World" lists the planets and shows a detail card for each one.

---

## 📁 Project structure

```
exoplanet-/
├── App.js                   # App entry: stack navigator (Home → Details)
├── app.json                 # Expo configuration
├── package.json             # JS dependencies and scripts
├── babel.config.js
├── screens/
│   ├── Home.js              # List of planets with distance from Earth
│   └── Details.js           # Detail card for one planet
├── assets/
│   └── planet_type/         # Images for Gas Giant, Terrestrial, Super Earth, Neptune Like
└── scraper/
    ├── updated_scraper.py   # Step 1: scrape the catalog list page
    ├── new_scraper.py       # Step 2: scrape each planet's detail page
    ├── scraper_2.py         # All-in-one: list + details → final.csv
    ├── updated_scraped_data.csv
    ├── new_scraped_data.csv
    └── final.csv
```

---

## 🕷️ Part 1: Web scraper

### How it works

The NASA catalog is loaded with JavaScript, so **Selenium** drives a real Chrome browser to open the page and move between pages. **BeautifulSoup** then parses the HTML. Each planet's detail page is static, so it's fetched with `requests`.

| Script | Reads | Writes | What it collects |
|---|---|---|---|
| `updated_scraper.py` | NASA catalog (page 1) | `updated_scraped_data.csv` | name, light years from Earth, mass, stellar magnitude, discovery date, detail-page link |
| `new_scraper.py` | `updated_scraped_data.csv` | `new_scraped_data.csv` | planet type, discovery date, mass, radius, orbital radius, orbital period, eccentricity, detection method |
| `scraper_2.py` | NASA catalog (pages 1–4) | `final.csv` | the list data and detail-page data merged into one row per planet |

### Setup

Requirements: **Python 3.8+** and **Google Chrome**.

```bash
cd scraper
python -m venv venv
venv\Scripts\activate          # Windows
# source venv/bin/activate     # macOS / Linux

pip install selenium beautifulsoup4 requests pandas
```

Selenium 4.6+ downloads a matching ChromeDriver automatically. On older versions, download [ChromeDriver](https://googlechromelabs.github.io/chrome-for-testing/) for your Chrome version and put it in the `scraper/` folder.

### Run

```bash
# Option A: two steps
python updated_scraper.py     # → updated_scraped_data.csv
python new_scraper.py         # → new_scraped_data.csv

# Option B: all in one
python scraper_2.py           # → final.csv
```

A Chrome window opens and pages through the catalog. Don't close it until the script finishes. To scrape more or fewer pages, change `range(1, 2)` / `range(1, 5)` in `scrape()`.

---

## 📱 Part 2: Mobile app

### Screens

- **Home ("Planets World"):** a scrollable list of planets, each showing its distance from Earth. Tap a planet to open its details.
- **Details:** a card with an image for the planet type, plus distance from Earth and from its sun, gravity, orbital period and speed, mass, radius, type, and specifications.

### Backend API

The app gets its data from a backend API. **The backend code is not in this repo.** The app expects:

| Endpoint | Response |
|---|---|
| `GET /` | `{ "data": [ { "name": ..., "distance_from_earth": ... }, ... ] }` |
| `GET /planet?name=<planet_name>` | `{ "data": { "name", "distance_from_earth", "distance_from_their_sun", "gravity", "orbital_period", "orbital_speed", "planet_mass", "planet_radius", "planet_type", "specifications": [...] } }` |

Set the API address in the `url` field of `screens/Home.js` and `screens/Details.js`:

- **Testing in a web browser:** `http://localhost:5000/`
- **Testing on a phone:** expose the server with a tunnel such as [ngrok](https://ngrok.com/) and use that https URL

### Run

Requirements: **Node.js** and the **Expo Go** app on your phone (optional).

```bash
npm install
npm start          # then scan the QR code with Expo Go
# or: npm run web / npm run android / npm run ios
```

You can also open the project in [Expo Snack](https://snack.expo.dev/) and run it in the browser.

---

## ⚠️ Known limitations

- The scrapers depend on the current HTML of the NASA site (CSS classes and XPaths). If NASA changes its layout, the selectors will need updating.
- `final.csv` has 11 header columns, but some rows contain more values, so a few columns can be shifted. Check the data before using it.
- On Windows, `scraper_2.py` writes blank lines between rows. Opening the file with `newline=""` fixes this.
- `Details.js` currently requests `/` instead of `/planet?name=...` (the correct URL is commented out). Switch to the commented line once the backend is running.

---

## 🛠️ Tech stack

**Scraper:** Python · Selenium · BeautifulSoup · Requests · Pandas
**App:** React Native · Expo · React Navigation · React Native Elements · Axios

## 🙏 Data source

Planet data comes from the [NASA Exoplanet Catalog](https://exoplanets.nasa.gov/exoplanet-catalog/).

## 👩‍💻 Author

**Rashneema Ameen**: [@RashneemaAmeen](https://github.com/RashneemaAmeen)
