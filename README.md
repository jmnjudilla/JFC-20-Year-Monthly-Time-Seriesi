# 📈 JFC Monthly Time Series — Power BI Stock Analysis Report

A comprehensive Power BI report analyzing **Jollibee Foods Corporation (JFC)** stock performance from **2000 to 2017**, built using the Alpha Vantage Monthly Adjusted Time Series API. The report covers trend analysis, growth cycles, crash detection, and market era segmentation across three interactive pages.

---

## 📊 Report Pages

### Page 1 — Trend Overview
Momentum signals, trend direction, and moving averages.

| Visual | Description |
|--------|-------------|
| KPI Cards | Current close price, market status (Bullish/Bearish), trend age, 6M EMA, 12M SMA |
| Trend Chart | Line chart overlaying close price, 6-Month EMA, and 12-Month SMA |
| Trend Lifespan | Clustered column chart showing the duration of each Bullish/Bearish episode |

### Page 2 — Growth & Risk Cycles
Historical performance, peak prices, and crash cycles.

| Visual | Description |
|--------|-------------|
| KPI Cards | All-time high (filter-aware), worst crash %, max price |
| Market Era Slicer | Button slicer to zoom all charts into a specific market era |
| Monthly Performance Grid | Matrix heatmap — Year × Month with conditional formatting (green = positive, red = negative) |

### Page 3 — Market Era Analysis
Stock behavior segmented by major economic eras.

| Visual | Description |
|--------|-------------|
| KPI Cards | Current era, monthly return %, trend status, months in trend |
| Era Bar Chart | Average monthly return by market era |
| Era Breakdown Table | Era name with corresponding year range |

---

## 🗂️ Data Model

### Tables

| Table | Type | Description |
|-------|------|-------------|
| `Monthly Adjusted Time Series` | Fact | Raw monthly OHLCV stock data from Alpha Vantage |
| `Calendar` | Dimension | Date table marked as official date table, dynamically driven by min/max dates in the fact table |
| `_Measures` | Measures | All DAX measures organized into display folders |
| `Page_Navigator` | Helper | Drives dynamic page header via slicer sync |

### Calendar Columns

| Column | Type | Description |
|--------|------|-------------|
| Date | Date | Primary key, marked as date table |
| Year | Integer | Calendar year |
| Month | Integer | Month number (sort key for Month Name) |
| Month Name | Text | Full month name, sorted by Month |
| Month Short | Text | 3-letter abbreviation, sorted by Month |
| Quarter | Text | Q1–Q4, sorted by Quarter Number |
| Quarter Number | Integer | Quarter sort key |
| Day | Integer | Day of month |
| Day Name | Text | Day of week name, sorted by Day of Week |
| Day of Week | Integer | Day of week number (Monday = 1) |
| Day of Year | Integer | Day of year (1–366) |
| Week of Year | Integer | ISO week number |
| Year-Month | Text | YYYY-MM format for sorting |
| Is Weekend | Boolean | True if Saturday or Sunday |
| Market Era | Text | Economic era label, sorted by Market Era Sort |
| Market Era Sort | Integer | Hidden sort key for Market Era (1–6) |

---

## 📐 DAX Measures (`_Measures` table)

### 📁 Moving Averages

| Measure | Description |
|---------|-------------|
| `6_Month_EMA` | 6-Month Exponential Moving Average for Line Chart axis context |
| `6_Month_EMA_Card` | Card-safe version of 6M EMA (uses TOPN, no WINDOW dependency) |
| `12_Month_SMA` | 12-Month Simple Moving Average for Line Chart axis context |
| `12_Month_SMA_Card` | Card-safe version of 12M SMA |

### 📁 Trend Analysis

| Measure | Description |
|---------|-------------|
| `Trend_Status` | 1 = Bullish (EMA > SMA), 0 = Bearish |
| `Macro_Trend_State` | Broad market signal based on EMA/SMA crossover |
| `Market_Status` | Text label: "BULLISH" or "BEARISH" |
| `Market_Status_Color` | Hex color: #16A34A (green) or #DC2626 (red) for conditional formatting |
| `Months_In_Trend` | Rolling count of consecutive months in current trend |
| `Trend_Age` | Card-safe trend duration counter formatted as "X months" |
| `Monthly_Return_Pct` | Month-over-month % change in closing price |
| `Trend_Episode_Duration` | Total months per distinct trend episode (for Trend Lifespan chart) |

### 📁 Price

| Measure | Description |
|---------|-------------|
| `Current_Close_Price` | Latest closing price within the current filter context |
| `Max_Price` | Absolute all-time high closing price (ignores all filters) |
| `All_Time_High` | Highest closing price within the current filter/slicer context |
| `Worst_Crash_Pct` | Largest peak-to-trough percentage drop within the selected period |

### 📁 Report UX

| Measure | Description |
|---------|-------------|
| `Page_Header` | Dynamic page title driven by Page_Navigator slicer |
| `Page_Subtitle` | Dynamic page subtitle driven by Page_Navigator slicer |
| `Selected_Era_Header` | Dynamic label showing selected era + year range (e.g. "Global Financial Crisis · 2008 – 2009") |
| `Current_Era` | Returns selected era name or "All Eras" when no slicer selection |

---

## 🏷️ Calculated Columns (`Monthly Adjusted Time Series` table)

| Column | Description |
|--------|-------------|
| `Trend_Episode_ID` | Sequential integer identifying each distinct Bullish/Bearish run |
| `Trend_Episode_Label` | Readable episode label (e.g. "Bullish #1", "Bearish #2") for chart X-axis |
| `Trend_Episode_Type` | "Bullish" or "Bearish" per row — used as Legend field in Trend Lifespan chart |

---

## 🕰️ Market Eras

| # | Era | Years | Context |
|---|-----|-------|---------|
| 1 | Dot-com Bust & Post-Asian Crisis Recovery | 2000–2002 | Tech bubble collapse; Philippines recovering from 1997 Asian Financial Crisis |
| 2 | Pre-GFC Bull Run | 2003–2007 | Global liquidity boom; PSEi surged; JFC expanded aggressively |
| 3 | Global Financial Crisis | 2008–2009 | Lehman collapse; global recession; Philippine peso volatility |
| 4 | Post-GFC Recovery | 2010–2012 | Central bank stimulus; PSEi rebounds; emerging market inflows |
| 5 | PH Economic Boom | 2013–2015 | PH GDP growth peaked ~7%; PSEi all-time highs; JFC regional expansion |
| 6 | Late Bull Market | 2016–2017 | Sustained growth; Duterte administration; JFC nearing dataset peak |

---

## 🔧 Data Source

- **Provider:** [Alpha Vantage](https://www.alphavantage.co/)
- **Endpoint:** `TIME_SERIES_MONTHLY_ADJUSTED`
- **Symbol:** `JFC` (Jollibee Foods Corporation, PSE)
- **Date Range:** December 1999 – July 2017
- **Frequency:** Monthly

> ⚠️ **Note:** The Alpha Vantage API key is embedded in the Power Query connection string. Remove or replace it before sharing this file publicly.

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| Power BI Desktop | Report development |
| Power Query (M) | Data transformation and Calendar table generation |
| DAX | All measures and calculated columns |
| Alpha Vantage API | Stock data source |
| Power BI MCP Server | Model management via Claude AI |

---

## 📁 Repository Structure

```
JFC-Monthly-Time-Series/
│
├── README.md                          # This file
└── screenshots/                       # Report page previews
    ├── page1-trend-overview.png
    ├── page2-growth-risk-cycles.png
    └── page3-market-era-analysis.png
```

---

## 🚀 Getting Started

1. Get a free API key from [alphavantage.co](https://www.alphavantage.co/support/#api-key)
2. In Power BI Desktop, go to **Transform Data → Data Source Settings** and plug in your API key
3. Click **Refresh** to load the latest data
4. Explore the three report pages

---

## 👤 Author

**Jeryl**
Built with assistance from Claude (Anthropic) via the Power BI MCP Server integration.
