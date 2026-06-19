# NYC Airbnb Market Analysis 

An interactive Tableau story exploring New York City's Airbnb listings — pricing patterns, neighbourhood noise levels, room type mix, review activity, and minimum-stay requirements across the city's five boroughs.

🔗 **File:** `Airbnb.twbx` (packaged workbook, includes extracted data — no separate data file needed)
📄 **Static export:** `Story_1.pdf`

---

## Overview

This project analyzes Airbnb listing data for New York City to understand how price, room type, neighbourhood noise, and guest review activity vary across boroughs and individual neighbourhoods. The workbook is built as a **six-page Tableau Story**, where each page tackles a specific angle of the dataset and the pages are designed to be read in sequence.

| # | Story Point | What it answers |
|---|---|---|
| 1 | **Price** | How do prices differ by room type, and how have they trended over time (2014–2019) by borough? |
| 2 | **Noise** | Which boroughs/neighbourhoods are noisiest, and where are listings located relative to noise level? |
| 3 | **Price Geo** | Where are high/medium/low-priced listings concentrated geographically? |
| 4 | **Room Type (Pt II)** | What's the split between Entire home/apt, Private room, and Shared room — and how does that interact with price tier? |
| 5 | **Review** | How does review volume relate to price, room type, and neighbourhood? |
| 6 | **Nights** | How do minimum-night requirements and average price relate across boroughs and room types? |

---

## Dataset

**Source table:** `spring23_final_train_with_price.csv` (embedded as a Tableau `.hyper` extract inside the `.twbx`)

| Field | Type | Description |
|---|---|---|
| `id` | Integer | Unique listing ID |
| `name` | String | Listing title |
| `host_id` | Integer | Unique host ID |
| `host_name` | String | Host's display name |
| `neighbourhood_group` | String | Borough (Bronx, Brooklyn, Manhattan, Queens, Staten Island) |
| `neighbourhood` | String | Specific neighbourhood (e.g., Astoria, Bushwick, Harlem) |
| `latitude` / `longitude` | Real | Geographic coordinates used for map layers |
| `room_type` | String | Entire home/apt, Private room, or Shared room |
| `price` | Real | Nightly listing price ($) |
| `minimum_nights` | Integer | Minimum required stay length |
| `number_of_reviews` | Integer | Total review count |
| `last_review` | Date | Date of most recent review |
| `reviews_per_month` | Real | Review frequency |
| `floor` | Integer | Floor level of the listing |
| `noise(dB)` | Real | Ambient noise level associated with the listing's location |

### Calculated fields

- **`Price Group`** — Buckets each listing into a price tier:
  ```
  IF [price] <= 100 THEN "Low"
  ELSEIF [price] <= 300 THEN "Medium"
  ELSE "High"
  END
  ```
- **`Correlation`** — `WINDOW_CORR(AVG([minimum_nights]), AVG([price]))`, used on the Nights page to quantify the relationship between minimum stay and average price per borough.

---

## Dashboard pages in detail

### 1. Price
![Price story page](<img width="1066" height="715" alt="image" src="https://github.com/user-attachments/assets/593c70d5-f73d-4c92-87b4-59d7b4dc5a18" />
)
- **Price By Room Type** — line chart of average price (2014–2019) split by room type.
- **Price Trending In Neighbor Groups Over Time** — a faceted time series (2014–2019) for each borough, with an interactive filter.
- **Average Price [For Graph 1–3]** — a shared reference line showing the overall average price trend, used to give context to the two charts above.
- A **Neighbourhood** and **Room Type** filter pane in the upper-left lets you drill into a specific area.

### 2. Noise
![Noise story page](https://claude.ai/api/9661c463-c364-47b5-bba7-c3f76b29c7ac/files/74b7cfb7-1cf4-488e-b88d-1e174617519e/preview)
- **Noise by Neighbourhood Group** — bar/treemap-style summary: Bronx has the highest average noise level (78.43 dB), followed by Brooklyn (69.11), Queens (62.45), Manhattan (56.22), and Staten Island (51.71 — quietest).
- **Noise by Neighbourhood** — same metric broken down to the neighbourhood level, sorted within each borough.
- **Noise Level-Map** — geographic plot of listings color-coded by noise level (light → dark red as dB increases), with a click-to-filter interaction tied to the borough/neighbourhood charts.

### 3. Price Geo
![Price Geo story page](images/03-price-geo.png)
- **Average Price Distribution** — a map of all listings colored by `Price Group` (High = red, Medium = green, Low = yellow), showing that high-priced listings cluster heavily in Manhattan.
- **Number of Neighbourhood in Different Price Group** — small-multiple bar charts (one row per price tier) showing how many neighbourhoods in each borough fall into Low/Medium/High price bands. Brooklyn and Manhattan dominate the Medium and High tiers; Brooklyn also leads the Low tier.

### 4. Room Type (Pt II)
![Room Type story page](images/04-room-type.png)
- **Number of Room Types by Price Group** — stacked bar chart showing listing counts per room type, segmented by price tier (e.g., Private room: 15,098 Medium-priced listings vs. 2,550 High).
- **% of Total Number of Rooms** — the same breakdown as percentages (e.g., Entire home/apt is 63.38% Medium, 26.32% Low, 10.30% High).
- A geographic map shows where Entire home/apt vs. Private room listings concentrate (Shared room is excluded from the legend/map view in this layout).

### 5. Review
![Review story page](images/05-review.png)
- **Price vs. Reviews Per Month by Room Type** — bar chart combining review-frequency buckets (1, 1.5, 2, 3, 4, 5, 7, 10+ reviews/month) with price, faceted by room type, with reference average lines.
- **Number of Reviews vs. Price by Neighbourhood** — scatter-style chart relating total review count to price for each neighbourhood, with reference averages (avg. reviews ≈ 49K-scale, varies by neighbourhood).
- **Review Counts by Neighbourhood** — horizontal bar chart ranking neighbourhoods by total review volume. **Bedford-Stuyvesant** and **Williamsburg** top the list (88K and 68K respectively), reflecting both listing density and popularity.
- A **Neighbourhood** and **Room Type** filter pane supports drill-down.

### 6. Nights
![Nights story page](images/06-nights.png)
- **Average minimum night** (map) and **Average price** (map) — two side-by-side geo views, each annotated with borough-level callouts combining `Room Type`, `Avg. Minimum Nights`, and `Avg. Price` (e.g., Manhattan Shared room averages 6.75 minimum nights; Manhattan Private room averages $104.90/night — the highest in the city).
- **Relationship** — a scatter plot of `Avg. Minimum Nights` vs. `Avg. Price` by borough, sized/colored by group, revealing a rough positive relationship: boroughs with higher average prices (Manhattan, Brooklyn) also tend to require longer minimum stays.

---

## Key insights

- **Manhattan commands the highest prices** but is also the quietest of the boroughs with substantial listing volume, while the **Bronx is the noisiest** borough despite having comparatively low average prices — noise level does not track with price.
- **Private rooms dominate listing volume** (15,098 in the Medium price tier alone) but **Entire home/apt listings command the highest share of "High" price tier** listings relative to their total volume.
- **Brooklyn neighbourhoods generate the most reviews** overall (Bedford-Stuyvesant and Williamsburg lead the city), suggesting high booking turnover and guest satisfaction density in these areas.
- **Minimum-night requirements scale with price across boroughs**: Staten Island (lowest avg. price, ~3.5 min nights) sits at one end and Manhattan/Brooklyn (highest avg. price, 5.5+ min nights) at the other, indicating hosts in pricier markets ask for longer commitments.

---

## How to use this workbook

1. Open `Airbnb.twbx` in **Tableau Desktop** or **Tableau Public** (the extract is packaged inside, so no separate data connection is required).
2. Navigate using the **story tabs** at the top of the screen: `Price → Noise → Price Geo → Room Type (Pt II) → Review → Nights`.
3. Use the **filter panes** (Neighbourhood, Room Type, Neighbourhood Group) on the Price, Noise, and Review pages to drill into specific areas.
4. Hover over map points and bars for **tooltips** with exact values; click marks on the Noise Level-Map to cross-filter the adjoining charts.

If you don't have Tableau installed, refer to `Story_1.pdf` for a static page-by-page snapshot of the same dashboard.

---

## Tools & tech

- **Tableau Desktop / Public** — dashboard authoring and story navigation
- **Tableau Hyper Extract** — embedded data engine (`.hyper` files inside the `.twbx`)
- **Mapbox / OpenStreetMap** — base map tiles for all geographic visualizations

---

## File structure

```
.
├── Airbnb.twbx          # Packaged Tableau workbook (data + dashboards)
├── Story_1.pdf           # Static PDF export of all six story pages
├── images/                # Screenshots used in this README
│   ├── 01-price.png
│   ├── 02-noise.png
│   ├── 03-price-geo.png
│   ├── 04-room-type.png
│   ├── 05-review.png
│   └── 06-nights.png
└── README.md             # This file
```
