# Smart City Analytics Dashboard — Complete Power BI Project Guide

A beginner-to-intermediate, interview-ready Power BI portfolio project. Light theme, iOS-style
typography, no animations, no AI/ML — pure business intelligence best practice.

**Files in this package**
- `SmartCity_PowerBI_Guide.md` — this document
- `data/SmartCity_SampleData.xlsx` — 7 sheets (3 dimensions + 4 facts), ~100 rows per fact table
- `data/dashboard_data.json` — the same data pre-aggregated (used only to generate the HTML preview)
- `LightTheme_iOS.json` — importable Power BI theme
- `SmartCity_Dashboard_Preview.html` — interactive, non-Power BI preview of all 5 pages, built from the real sample data

---

## 1. Project Overview

**Goal:** simulate Pune City's operations dashboard — traffic, crime, air pollution, and water
supply across all 15 real PMC ward offices — the way a municipal analytics team would report it
to city leadership. Data is year-to-date for 2026 (Jan 1 – Jul 20), the most recent data
available.

### 1.1 Data sources and calibration — read this before presenting the project

**This is a simulated (synthetic) dataset, not a direct extract from a government system.**
Ward-level, daily-granularity civic data of this kind generally isn't published by PMC, Pune
Police, or the Pollution Control Board, so — as is standard practice for a portfolio project —
the individual row values are randomly generated. What makes it *realistic* rather than arbitrary
is that every range and total was calibrated against real, currently reported figures found via
web research:

| Metric | Real figure used to calibrate | Source |
|---|---|---|
| Zones / ward offices | Pune Municipal Corporation has **15 administrative Ward Offices** (Kshetriya Karyalay), each overseeing several electoral prabhags | PMC structure / Wikipedia — Pune Municipal Corporation |
| City-wide AQI, 2026 | Annual average AQI ≈ **107**; winter (Jan–Feb) peaks near 150–200, monsoon (Jun–Jul) drops to 45–70 — this dataset's monthly AQI baseline follows that same seasonal curve | aqi.in historical analysis, Pune, 2026 |
| Water supply | PMC draws roughly **1,350–1,500 MLD** city-wide from the Khadakwasla dam system (rising to ~1,670 MLD in summer); this dataset's 15-zone daily supply sums to a comparable citywide range | PMC Water Supply Department reporting / local news coverage, 2025–2026 |
| Non-revenue water (leakage) | Commonly cited in the 15–20% range for PMC's network | PMC water-audit reporting |
| Registered vehicles | Pune RTO region: **~5.08 million** registered vehicles (Mar 2025) — used as general context for plausible daily zone-level traffic volumes, not a direct per-row figure | Maharashtra Transport Dept. statistics, 2024–25 |

Crime case volumes don't have an equivalent public per-ward daily figure, so those ranges are
modeled at a plausible city scale rather than calibrated to a specific published number — be
transparent about that distinction if asked in an interview: "the AQI and water figures are
calibrated to real published city statistics; the crime table is a realistic simulation since
ward-level daily crime data isn't public."

**If you need the project to run on genuinely real, government-sourced data** (not just
calibrated ranges), the next step would be requesting datasets directly from `data.gov.in`,
PMC's open data portal, or filing an RTI request — that's beyond what's feasible to compile into
a clean star-schema model in this format, but it's the honest path to a fully "real data" version
of this same project.

**Modules**
1. Executive Dashboard (summary across all domains)
2. Traffic Analytics
3. Crime Analytics
4. Air Pollution Analytics
5. Water Consumption Analytics

**Design rules used throughout:** light background (#F2F2F7), white rounded cards, iOS system
accent colors (blue/green/orange/purple/teal/red), Segoe UI as the typography stand-in for SF Pro
(see Section 9), no gradients, no 3D, no animated transitions, one clear chart per business
question.

---

## 2. Module Specifications

### 2.1 Executive Dashboard
- **Business objective:** give city leadership a one-screen read on urban health — is traffic
  flowing, is crime under control, is air breathable, is water reliable.
- **KPIs:** Total Vehicles Tracked, Total Crimes Reported, Crime Resolution Rate, Average AQI,
  Total Water Supplied (MLD), Average Traffic Speed, Total Accidents.
- **Charts:** monthly traffic trend (line), monthly crime trend (line), average AQI trend (line),
  population by region (donut).
- **Filters:** Year, Quarter, Region (all from `Dim_Date` / `Dim_Location`, applied via the
  report-level filter pane so every page inherits them).
- **Insights:** which quarter had the worst congestion-and-crime combination; whether AQI trends
  track with traffic volume (a classic hypothesis city analysts test); which region is
  under-resourced relative to its population.

### 2.2 Traffic Analytics
- **Objective:** identify congestion hotspots and correlate traffic load with accidents.
- **KPIs:** Total Vehicles, Average Speed, Total Accidents, % Severe Congestion Readings.
- **Charts:** vehicle volume trend (line), congestion level split (donut), top zones by volume
  (horizontal bar), average speed by zone (horizontal bar).
- **Filters:** Zone, Congestion Level, Month.
- **Insights:** zones needing signal-timing or lane interventions; whether low average speed
  correlates with higher accident counts; seasonal peaks (e.g., festival months).

### 2.3 Crime Analytics
- **Objective:** track case volume, resolution performance, and geographic concentration.
- **KPIs:** Total Crimes Reported, Total Resolved, Resolution Rate, Pending Cases.
- **Charts:** cases-reported trend (line), status breakdown (donut), crimes by type (horizontal
  bar), top zones by case count (horizontal bar).
- **Filters:** Zone, Crime Type, Status, Month.
- **Insights:** which crime type is rising and needs a task force; which zone has the lowest
  resolution rate (resourcing gap); whether case backlog is growing month over month.

### 2.4 Air Pollution Analytics
- **Objective:** monitor air quality against health thresholds and flag zones needing
  intervention.
- **KPIs:** Average AQI, Average PM2.5, Average PM10, % Poor/Severe Readings.
- **Charts:** AQI trend (line), AQI category distribution (donut), average AQI by zone
  (horizontal bar), PM2.5 vs PM10 by zone (multi-line/combo).
- **Filters:** Zone, AQI Category, Month.
- **Insights:** which zones consistently breach "Poor"/"Severe" thresholds; seasonal pollution
  spikes (e.g., winter inversion months); whether PM2.5 or PM10 is the dominant pollutant per
  zone, which changes the mitigation strategy (vehicle emissions vs. construction dust).

### 2.5 Water Consumption Analytics
- **Objective:** track supply reliability, demand, and non-revenue water (leakage).
- **KPIs:** Total Supply (MLD), Total Consumption (MLD), Total Leakage (MLD), Leakage % of
  Supply, Total Complaints.
- **Charts:** supply vs. consumption trend (combo/multi-line), consumption trend (line), top
  zones by consumption (horizontal bar), leakage share (donut).
- **Filters:** Zone, Month.
- **Insights:** zones where consumption is approaching supply capacity (future shortage risk);
  zones with disproportionate leakage (infrastructure investment priority); whether complaint
  volume tracks with leakage or with the supply-consumption gap.

---

## 3. Data Model

### 3.1 Star Schema

```
                        ┌───────────────┐
                        │   Dim_Date    │
                        │  DateKey (PK) │
                        └───────┬───────┘
                                │
        ┌───────────────┬──────┴──────┬───────────────┐
        │               │             │               │
 ┌──────▼─────┐  ┌──────▼─────┐ ┌─────▼──────┐  ┌──────▼─────┐
 │Fact_Traffic│  │ Fact_Crime │ │Fact_Pollut.│  │ Fact_Water │
 └──────┬─────┘  └──────┬─────┘ └─────┬──────┘  └──────┬─────┘
        │               │             │                │
        └───────┬───────┴──────┬──────┴────────┬───────┘
                 │              │               │
          ┌──────▼──────┐┌─────▼──────┐
          │Dim_Location ││Dim_Department│
          │LocationID(PK)││DepartmentID(PK)│
          └─────────────┘└─────────────┘
```

Every fact table connects to `Dim_Date` (1-to-many), `Dim_Location` (1-to-many), and
`Dim_Department` (1-to-many). This is a textbook **star schema**: dimensions surround a central
fact, all relationships are single-direction one-to-many, and there are no dimension-to-dimension
or fact-to-fact relationships. This is the layout Power BI's engine (VertiPaq) is optimized for,
and it's what interviewers expect to see explained.

### 3.2 Tables and Keys

| Table | Type | Primary Key | Row Count |
|---|---|---|---|
| Dim_Date | Dimension | DateKey (yyyymmdd int) | 201 |
| Dim_Location | Dimension | LocationID | 15 |
| Dim_Department | Dimension | DepartmentID | 5 |
| Fact_Traffic | Fact | TrafficID | 100 |
| Fact_Crime | Fact | CrimeID | 100 |
| Fact_Pollution | Fact | PollutionID | 100 |
| Fact_Water | Fact | WaterID | 100 |

**Foreign keys:** every fact table carries `DateKey`, `LocationID`, and `DepartmentID`, each
pointing back to the matching dimension's primary key. `DepartmentID` is fixed per fact table
(Traffic→Traffic Police, Crime→City Police, Pollution→Pollution Control Board,
Water→Water Supply Department) which mirrors how real municipal data is siloed by owning agency.

**The 15 zones in `Dim_Location`** are Pune's real PMC administrative Ward Offices, grouped into
5 regions:

| Region | Ward Offices |
|---|---|
| Central | Ghole Road, Tilak Road, Kasba-Vishrambagwada, Bhawani Peth, Dhole Patil Road |
| North | Aundh, Yerwada |
| West | Kothrud-Bawdhan, Warje-Karvenagar |
| South | Sahakarnagar, Bibwewadi, Dhankawadi-Katraj, Kondhwa-Wanawadi |
| East | Nagar Road, Hadapsar |

**Why `Dim_Date` has 201 rows and not ~100:** a date dimension must be a *complete, continuous
calendar* — every single day in the reporting window, not a sample of the days that happen to
appear in the fact tables. This project reports **year-to-date 2026** (Jan 1 – Jul 20), so
`Dim_Date` has one row per day in that window (201 days). If you filter to a week with no events,
a partial date table would break trends and "no data" would look identical to "missing dates."
Mark this table as the official **Date Table** in Power BI (Table tools → Mark as Date Table)
before writing any time-intelligence DAX — `TOTALYTD`, `DATEADD`, `SAMEPERIODLASTYEAR` all depend
on it. (When you refresh with a full year of data later, this table extends the same way —
one row per calendar day.)

### 3.3 Relationship Settings (for every fact-to-dimension link)
- Cardinality: **One to Many** (dimension = one side, fact = many side)
- Cross-filter direction: **Single** (dimension filters fact, not the reverse) — this is the
  standard, safest setting and avoids ambiguous filter paths.
- Active: all relationships should be active (no need for role-playing dimensions in this
  version, since there's only one date field per fact table).

---

## 4. Power Query Transformations (Step by Step)

Apply these in Power Query Editor (Home → Transform Data) before loading:

1. **Remove duplicates:** select the ID column (e.g., `TrafficID`) → right-click → *Remove
   Duplicates*. Do this per fact table before merging anything else in, since a duplicate ID
   would double-count every downstream measure.
2. **Handle null values:**
   - Numeric fields (e.g., `AvgSpeedKmph`): Transform → *Replace Values* → replace `null` with 0,
     or use *Fill Down* if the null represents a carried-forward reading.
   - Text/category fields (e.g., `Status`): replace null with `"Unknown"` so it appears as its own
     slicer category rather than silently vanishing from totals.
3. **Change data types:** explicitly set every column — `DateKey` as Whole Number (or Date, if you
   keep a real date column), IDs as Text, measures like `VehicleCount`/`AQI` as Whole Number,
   `AvgSpeedKmph`/`PM25` as Decimal Number. Power Query's auto-detected types are a starting
   guess, not a guarantee — always verify manually.
4. **Create calculated columns (in Power Query, not DAX, when the logic is row-level and static):**
   - `AQICategory` in `Fact_Pollution`: conditional column bucketing AQI into
     Good/Satisfactory/Moderate/Poor/Severe (already included pre-built in the sample data —
     shown here as the pattern to follow for other buckets).
   - `IsWeekend` in `Dim_Date`: `Date.DayOfWeek([Date]) >= 5`.
5. **Merge queries:** used to bring a dimension attribute onto a fact table for a calculated
   column (e.g., pulling `Region` onto `Fact_Traffic` for a static export) — Home → Merge Queries
   → match `LocationID` to `LocationID`, Join Kind = Left Outer, then expand only the needed
   column. In the finished data *model*, you don't merge fact and dimension tables — you keep them
   separate and let relationships do the join at query time. Merge is for one-off column
   enrichment, not for building the model.
6. **Append queries:** used when the same fact type comes from multiple source files (e.g., one
   CSV export per month) — Home → Append Queries → stack them into a single table with identical
   columns before loading. Not needed for this single-workbook version, but this is the step to
   use if you later add more months as separate files.
7. **Close & Apply**, then verify row counts in the Fields pane match what you expect (100 per
   fact table, 201 for Dim_Date, 15 for Dim_Location, 5 for Dim_Department).

---

## 5. DAX Measures

Create a dedicated **Measures** table (New Table → name it `_Measures`, delete the placeholder
column) and put every measure below inside it, organized by module. This keeps measures out of
the physical fact tables, which is the standard clean-model convention.

```dax
Total Vehicles = SUM(Fact_Traffic[VehicleCount])
```
Sums vehicle counts across whatever filter context is active (a zone, a month, a whole year).

```dax
Average Speed = AVERAGE(Fact_Traffic[AvgSpeedKmph])
```
Simple average of the speed column; recalculates per slicer/filter selection.

```dax
Total Accidents = SUM(Fact_Traffic[AccidentCount])
```

```dax
Total Crimes = SUM(Fact_Crime[CasesReported])
```

```dax
Total Resolved = SUM(Fact_Crime[CasesResolved])
```

```dax
Crime Rate =
DIVIDE([Total Resolved], [Total Crimes], 0)
```
`DIVIDE` is used instead of `/` because it safely returns 0 (the third argument) instead of an
error when `[Total Crimes]` is 0 — always guard denominators this way.

```dax
Average AQI = AVERAGE(Fact_Pollution[AQI])
```

```dax
Water Consumption = SUM(Fact_Water[ConsumptionMLD])
```

```dax
Total Water Supply = SUM(Fact_Water[SupplyMLD])
```

```dax
Leakage % =
DIVIDE(SUM(Fact_Water[LeakageMLD]), [Total Water Supply], 0)
```

```dax
Previous Month Vehicles =
CALCULATE([Total Vehicles], DATEADD(Dim_Date[Date], -1, MONTH))
```
`DATEADD` shifts the current filter context back one month along the marked date table, then
`CALCULATE` re-evaluates `[Total Vehicles]` inside that shifted context.

```dax
Monthly Growth % =
DIVIDE([Total Vehicles] - [Previous Month Vehicles], [Previous Month Vehicles], 0)
```
Classic month-over-month growth pattern: (this period − last period) ÷ last period.

```dax
YTD Vehicles =
TOTALYTD([Total Vehicles], Dim_Date[Date])
```
Accumulates `[Total Vehicles]` from the start of the fiscal/calendar year up to the latest date
in the current filter context — requires `Dim_Date` to be marked as the official date table.

```dax
Vehicles % Change vs Last Month =
VAR CurrentVal = [Total Vehicles]
VAR PreviousVal = [Previous Month Vehicles]
RETURN
DIVIDE(CurrentVal - PreviousVal, PreviousVal, 0)
```
Same idea as Monthly Growth %, written with `VAR` for readability — the pattern you'll reuse for
crime, AQI, or water percentage-change cards.

```dax
Severe Congestion % =
DIVIDE(
    CALCULATE(COUNTROWS(Fact_Traffic), Fact_Traffic[CongestionLevel] = "Severe"),
    COUNTROWS(Fact_Traffic),
    0
)
```
`CALCULATE` overrides the filter context to count only "Severe" rows, then divides by all rows —
the general pattern for any "% of readings that meet a condition" KPI.

**Explaining DAX in an interview, in one line each:** `SUM`/`AVERAGE` aggregate a column;
`CALCULATE` changes the filter context a measure runs in; `DATEADD`/`TOTALYTD` are time
intelligence that require a proper marked date table; `DIVIDE` is the safe division function;
`VAR...RETURN` makes multi-step formulas readable and avoids repeating the same sub-expression.

---

## 6. Building Each Dashboard Page — Step by Step

General page setup (every page): Page size 16:9 (1280×720), background `#F2F2F7`, all visuals in
white cards with 14px corner radius and a 1px `#E5E5EA` border (import `LightTheme_iOS.json` via
View → Themes → Browse for themes to apply this automatically to every new visual).

### 6.1 Executive Dashboard
| Visual | Chart type | Field wells | Formatting notes |
|---|---|---|---|
| KPI row (×6) | Card | Values: `[Total Vehicles]`, `[Total Crimes]`, `[Crime Rate]`, `[Average AQI]`, `[Total Water Supply]`, `[Average Speed]` | Callout font 28pt semibold, category label 11pt gray |
| Traffic trend | Line chart | Axis: `Dim_Date[MonthName]`, Values: `[Total Vehicles]` | Sort axis by `Dim_Date[Month]` (not alphabetically), no data labels, single blue line, no markers |
| Crime trend | Line chart | Axis: `Dim_Date[MonthName]`, Values: `[Total Crimes]` | Same sort rule, red line |
| AQI trend | Line chart | Axis: `Dim_Date[MonthName]`, Values: `[Average AQI]` | Orange line |
| Population by region | Donut chart | Legend: `Dim_Location[Region]`, Values: `Dim_Location[Population]` | Legend bottom, data labels off (tooltip only) |
| Slicers | Slicer (dropdown) | `Dim_Date[Year]`, `Dim_Date[Quarter]`, `Dim_Location[Region]` | Place top-right, sync across all 5 pages via View → Sync Slicers |

### 6.2 Traffic Analytics
| Visual | Chart type | Field wells | Formatting notes |
|---|---|---|---|
| KPI row (×4) | Card | `[Total Vehicles]`, `[Average Speed]`, `[Total Accidents]`, `[Severe Congestion %]` | |
| Volume trend | Line chart | Axis: `Dim_Date[MonthName]`, Values: `[Total Vehicles]` | |
| Congestion split | Donut chart | Legend: `Fact_Traffic[CongestionLevel]`, Values: Count of rows | Order legend Low→Severe manually via sort column |
| Top zones | Bar chart (horizontal) | Axis: `Dim_Location[ZoneName]`, Values: `[Total Vehicles]` | Sort descending, top N filter = 8 |
| Speed by zone | Bar chart (horizontal) | Axis: `Dim_Location[ZoneName]`, Values: `[Average Speed]` | Green bars, sort descending |
| Detail table | Table | `TrafficID`, `ZoneName`, `VehicleCount`, `AvgSpeedKmph`, `CongestionLevel`, `AccidentCount` | Conditional formatting: color scale on `AvgSpeedKmph` (red=slow, green=fast) |
| Slicer | Slicer | `Dim_Location[ZoneName]` | |

### 6.3 Crime Analytics
| Visual | Chart type | Field wells | Formatting notes |
|---|---|---|---|
| KPI row (×4) | Card | `[Total Crimes]`, `[Total Resolved]`, `[Crime Rate]`, Pending (a quick measure: Total − Resolved) | |
| Cases trend | Line chart | Axis: `Dim_Date[MonthName]`, Values: `[Total Crimes]` | |
| Status breakdown | Donut chart | Legend: `Fact_Crime[Status]`, Values: Count of rows | |
| Crimes by type | Bar chart (horizontal) | Axis: `Fact_Crime[CrimeType]`, Values: `[Total Crimes]` | Sort descending |
| Top zones | Bar chart (horizontal) | Axis: `Dim_Location[ZoneName]`, Values: `[Total Crimes]` | |
| Detail table | Table | `CrimeID`, `ZoneName`, `CrimeType`, `CasesReported`, `CasesResolved`, `Status` | Conditional formatting: icon set or color on `Status` |
| Slicer | Slicer | `Fact_Crime[Status]`, `Fact_Crime[CrimeType]` | |

### 6.4 Air Pollution Analytics
| Visual | Chart type | Field wells | Formatting notes |
|---|---|---|---|
| KPI row (×4) | Card | `[Average AQI]`, avg PM2.5, avg PM10, `%` Poor/Severe (quick measures) | |
| AQI trend | Line chart | Axis: `Dim_Date[MonthName]`, Values: `[Average AQI]` | Add a constant reference line at AQI=100 (Analytics pane) to visually flag the "Moderate" threshold |
| Category distribution | Donut chart | Legend: `Fact_Pollution[AQICategory]`, Values: Count of rows | |
| AQI by zone | Bar chart (horizontal) | Axis: `Dim_Location[ZoneName]`, Values: `[Average AQI]` | Sort descending — worst zones on top |
| PM2.5 vs PM10 | Line chart, 2 lines (or combo) | Axis: `Dim_Location[ZoneName]`, Values: avg PM2.5 and avg PM10 | Legend distinguishes the two pollutants |
| Detail table | Table | `PollutionID`, `ZoneName`, `AQI`, `PM25`, `PM10`, `AQICategory` | Color scale on `AQI` |
| Slicer | Slicer | `Fact_Pollution[AQICategory]` | |

### 6.5 Water Consumption Analytics
| Visual | Chart type | Field wells | Formatting notes |
|---|---|---|---|
| KPI row (×4) | Card | `[Total Water Supply]`, `[Water Consumption]`, Total Leakage (quick measure), `[Leakage %]` | |
| Supply vs consumption | Combo / 2-line chart | Axis: `Dim_Date[MonthName]`, Values: sum Supply and sum Consumption | Two distinct colors, legend bottom |
| Consumption trend | Line chart | Axis: `Dim_Date[MonthName]`, Values: `[Water Consumption]` | |
| Top zones | Bar chart (horizontal) | Axis: `Dim_Location[ZoneName]`, Values: `[Water Consumption]` | |
| Leakage share | Donut chart | Two categories: Delivered vs Leakage | Red slice for leakage draws the eye |
| Detail table | Table | `WaterID`, `ZoneName`, `SupplyMLD`, `ConsumptionMLD`, `LeakageMLD`, `ComplaintsReceived` | |
| Slicer | Slicer | `Dim_Location[ZoneName]` | |

**Tooltips (all pages):** keep default tooltips on, add the underlying raw value (not just the
rounded display value) so a viewer hovering a bar sees the exact number.

**Cross-filtering:** leave default interactions on (click a bar → filters the rest of the page) —
this is the single most "wow" interactive moment in a live demo and costs nothing to set up.

---

## 7. Business Decisions This Dashboard Supports

- **Infrastructure investment prioritization** — which zones need traffic signal upgrades, water
  pipeline repairs, or pollution controls first, based on data rather than assumption.
- **Police resource allocation** — redirecting patrols toward zones/crime types with low
  resolution rates or rising case volume.
- **Public health advisories** — issuing zone-specific air quality warnings when AQI trends into
  Poor/Severe.
- **Utility planning** — flagging zones where water consumption is approaching supply capacity,
  ahead of a shortage.
- **Executive reporting** — a single page leadership can review monthly without touching the
  underlying detail pages.

---

## 8. Folder Structure

```
SmartCity-PowerBI-Project/
├── PBIX/
│   └── SmartCity_Analytics_Dashboard.pbix        (you build this in Power BI Desktop)
├── Data/
│   └── SmartCity_SampleData.xlsx
├── Theme/
│   └── LightTheme_iOS.json
├── Docs/
│   ├── SmartCity_PowerBI_Guide.md
│   ├── DataDictionary.md                          (optional: column-by-column definitions)
│   └── Screenshots/                                (export each page as PNG once built)
├── Preview/
│   └── SmartCity_Dashboard_Preview.html
└── README.md
```

## 9. Dataset Structure (Summary)

| Table | Columns |
|---|---|
| Dim_Date | DateKey, Date, Day, Month, MonthName, Quarter, Year, WeekdayName, IsWeekend |
| Dim_Location | LocationID, City, ZoneName, Region, Population, AreaKmSq |
| Dim_Department | DepartmentID, DepartmentName, HeadOffice |
| Fact_Traffic | TrafficID, DateKey, LocationID, VehicleCount, AvgSpeedKmph, CongestionLevel, AccidentCount, DepartmentID |
| Fact_Crime | CrimeID, DateKey, LocationID, CrimeType, CasesReported, CasesResolved, Status, DepartmentID |
| Fact_Pollution | PollutionID, DateKey, LocationID, AQI, PM25, PM10, CO2Level, NoiseLevelDb, AQICategory, DepartmentID |
| Fact_Water | WaterID, DateKey, LocationID, SupplyMLD, ConsumptionMLD, LeakageMLD, ComplaintsReceived, DepartmentID |

## 10. Dashboard Layout (Page Wireframe, applies to all 5 pages)

```
┌─────────────────────────────────────────────────────────┐
│  Page title            Slicers: Year | Quarter | Region  │
├───────────┬───────────┬───────────┬───────────┬─────────┤
│  KPI card │  KPI card │  KPI card │  KPI card │ (KPI card)│
├───────────────────────────────┬───────────────────────────┤
│  Primary trend line chart      │  Category donut chart      │
├───────────────────────────────┼───────────────────────────┤
│  Zone comparison bar chart     │  Secondary bar/line chart  │
├─────────────────────────────────────────────────────────┤
│  Detail table (drillable rows)                            │
└─────────────────────────────────────────────────────────┘
```

## 11. Font Note — "iOS style" in Power BI

Power BI Desktop cannot embed San Francisco (Apple's system font) directly — it isn't licensed
for Windows, and Power BI's font picker only lists fonts installed on the machine. Two options:

1. **Cross-platform safe (used in the theme file):** `Segoe UI` / `Segoe UI Semibold`. It shares
   SF Pro's proportions closely enough that most viewers read it as "the same clean system
   font" — this is what ships in `LightTheme_iOS.json`.
2. **If you have SF Pro installed** (e.g., you downloaded it from Apple's developer site or use a
   Mac with Power BI Desktop... note Power BI Desktop is Windows-only, so this applies if you've
   manually installed SF Pro on Windows): open `LightTheme_iOS.json`, replace every
   `"fontFace": "Segoe UI..."` value with `"SF Pro Display"` / `"SF Pro Text"`, re-import the
   theme.

The HTML preview in this package uses the browser's native `-apple-system` stack, so on a Mac or
iPhone it renders in true SF Pro automatically, and falls back to Segoe UI on Windows.

---

## 12. Interview Questions Based on This Project

1. Why did you choose a star schema instead of a single flat table?
2. Walk me through your `Dim_Date` table — why does it need every calendar day, not just the days
   with data?
3. What's the difference between a calculated column and a measure, and where did you use each?
4. Explain what `CALCULATE` does that `SUM` alone cannot.
5. Why use `DIVIDE()` instead of the `/` operator?
6. How would you handle a scenario where a zone appears in `Fact_Traffic` but not in
   `Dim_Location`? (Answer: relationship shows blank/unmatched row — fix at the source or add a
   catch-all "Unknown Zone" row.)
7. How did you decide cross-filter direction on your relationships, and when would you use
   bi-directional filtering instead?
8. What's your approach to a KPI whose value should never be negative or exceed 100% — how do you
   guard against a broken measure?
9. If leadership asked for a 6th module tomorrow (say, "Waste Management"), what's your process
   for extending this model without breaking the existing pages?
10. How do you decide what goes on the Executive page versus a detail page?

## 13. Resume Description

> **Smart City Analytics Dashboard — Power BI** — Designed and built a 5-page business
> intelligence dashboard modeling traffic, crime, air pollution, and water consumption data for a
> Pune City, year-to-date 2026. Built a star-schema data model (3 dimension tables, 4 fact
> tables) from a ~400-row realistic dataset, applying Power Query transformations (deduplication,
> null handling, type standardization, calculated columns) and 12+ DAX measures including
> time-intelligence (YTD, month-over-month growth). Delivered a custom light-theme, iOS-inspired
> visual design with cross-filtered KPI cards, trend lines, and drillable detail tables.

## 14. GitHub README Description

> **Smart City Analytics Dashboard**
> A beginner-to-intermediate Power BI portfolio project modeling Pune City's municipal analytics
> across all city zones — Traffic, Crime, Air Pollution, and Water Consumption, plus a unified
> Executive summary — for year-to-date 2026. Built on a clean star-schema data model with
> realistic sample data, documented Power Query steps, and fully explained DAX measures. Designed
> with a light, iOS-inspired visual theme and no unnecessary animation — built to be explained
> clearly in a technical interview.
>
> **Contents:** `/PBIX` the Power BI file · `/Data` sample dataset (.xlsx) · `/Theme` importable
> Power BI theme (.json) · `/Docs` full build guide · `/Preview` static HTML preview of all 5
> pages.

---

## 15. Build Checklist

- [ ] Import `SmartCity_SampleData.xlsx` (Get Data → Excel)
- [ ] Apply Power Query steps from Section 4 to every table
- [ ] Build relationships per Section 3.3 (verify in Model view)
- [ ] Mark `Dim_Date` as Date Table
- [ ] Create `_Measures` table and add every DAX measure from Section 5
- [ ] Import `LightTheme_iOS.json` (View → Themes → Browse for themes)
- [ ] Build each page per Section 6, page by page
- [ ] Turn on Sync Slicers across all 5 pages
- [ ] Export each page as PNG for your README/portfolio screenshots
- [ ] Publish or save final `.pbix` into `/PBIX`
