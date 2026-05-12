# sales-revenue-dashboard
# 📊 SalesPulse — Sales & Revenue Analysis Dashboard

A fully client-side, zero-dependency sales analytics dashboard. No server required — open `index.html` in any browser and start analyzing.

---

## 📁 Project Structure

```
sales-dashboard/
├── index.html              ← Main dashboard (open this in your browser)
├── README.md               ← This file
├── sales_import.csv        ← Sample data (34 rows across FY2023, FY2024, FY2025)
└── sales_import.xlsx       ← Same data in Excel format
```

---

## 🚀 Quick Start

### Option 1 — Open directly
1. Double-click `index.html` — it opens in your browser instantly.
2. The dashboard loads with built-in demo data automatically (8 sample products, FY2024).

### Option 2 — Import your own data
1. Click **"Import CSV / Excel"** in the top bar.
2. Select your `.csv` or `.xlsx` file.
3. The dashboard detects all years in your file and refreshes immediately.
4. Use the **FY Year** dropdown to switch between years.

---

## 📋 Required File Columns

When importing your own CSV or Excel file, include these columns (names are flexible — fully case-insensitive, spaces and underscores ignored):

| Column | Accepted Names | Example |
|--------|---------------|---------|
| Product name | `product`, `name`, `productname`, `item` | ProMax Laptop |
| Region | `region`, `area`, `zone` | North |
| Sale amount | `amount`, `sales`, `revenue`, `value`, `price` | 3200 |
| Order status | `status`, `state` | completed |
| Category | `category`, `cat`, `type` | electronics |
| Fiscal year | `year`, `fy`, `fiscalyear`, `fiscalyr` | 2025 |
| Month | `month`, `mon` | 7 |
| Customer name | `customer`, `customername`, `customerid`, `client`, `buyer` | Ravi Kumar |

> **Customer column:** Optional but recommended. When present, the Customers section uses real customer names to calculate unique customers, new customers, and retention rate accurately. Without it, unique product names are used as a fallback proxy.
>
> **Status values:** `completed`, `pending`, `refunded` — used for badge colours in the transactions table.
>
> **Year column:** Accepts plain numbers (`2024`) or prefixed formats (`FY2024`, `FY 2024`). If omitted, all rows default to FY2024.
>
> **Month column:** Numbers 1–12. Used to sort Recent Transactions newest-first and to plot the Monthly Revenue Trend chart.

---

## 🎛️ Dashboard Features

### KPI Cards
| Card | What it shows |
|------|--------------|
| Total Revenue | Sum of all `amount` values for the active year + filters |
| Total Orders | Row count matching the active year + filters |
| Avg Order Value | Total Revenue ÷ Total Orders |
| Refund Rate | % of orders with refunded status — calculated from your data, with YoY delta |

Each card shows a YoY delta vs the previous year. When imported data includes the prior year, the delta is calculated from real data; otherwise a regional estimate is used.

### Charts
- **Monthly Revenue Trend** — Line chart comparing the active year vs the prior year, plotted month by month.
- **Revenue by Category** — Doughnut chart showing each category's % share of revenue for the active year and region.
- **Top Products by Revenue** — Horizontal bar chart of the top 6 products by total revenue.
- **Recent Transactions** — Scrollable table of all transactions sorted newest month first, with colour-coded status badges.

### Sidebar Sections
| Section | What it shows |
|---------|--------------|
| Dashboard | KPI cards + all 4 charts |
| Revenue | Total, best region, YoY growth, monthly avg + region breakdown table |
| Orders | Order count, completion/pending/refunded breakdown + full transaction table |
| Products | Top seller, avg revenue per product, top category + full product table |
| Customers | Unique customers, new customers, retention rate, avg LTV — all derived from your imported data |
| Reports | Export links for Revenue, Orders, Products, Customer Insights reports |

### Customers Section (data-driven)
When you import a file with a `customer` column, all four metrics are calculated from real data:

| Metric | How it's calculated |
|--------|-------------------|
| Total Customers | Count of unique customer names in the active year + filters |
| New Customers | Customers who appear this year but were absent in the prior year |
| Retention Rate | % of last year's customers who also bought this year |
| Avg LTV | Total revenue ÷ unique customers for the active year |

The region breakdown table is also rebuilt dynamically from the regions in your file.

> Without a `customer` column, unique product names are used as a fallback — numbers will still be real (not demo data), just less precise.

### Filters
| Filter | Behaviour |
|--------|-----------|
| Region | All / or regions found in your file |
| Fiscal Year | Rebuilt from your file after import |
| Category | All / or categories found in your file |

All three filters update every chart, KPI, table, and the Customers section simultaneously.

---

## 📥 Import Behaviour

- Accepts `.csv`, `.xlsx`, and `.xls` files.
- Column detection is fully automatic — no fixed column order required.
- All years in the file are loaded at once and separated into per-year buckets.
- After import the Year dropdown is rebuilt to show every year found (e.g. FY2023, FY2024, FY2025).
- The most recent year is selected automatically.
- Importing the same file again safely overwrites — no duplicates are created.
- Region, Category, and Year dropdowns are rebuilt from the real values in your file.

---

## 📤 Export Behaviour

- Clicking **Export** when data has been imported downloads **all years combined** as `sales_import_all.csv`.
- The exported file includes `Year` and `Month` columns so it can be re-imported without data loss.
- Clicking **Export** on the default demo data exports only the current filtered view as `sales_data_FY<year>.csv`.
- A toast notification confirms the row count and filename after every export.

---

## 🌐 Supported Browsers

Works in all modern browsers:
- Chrome 90+ ✅
- Firefox 88+ ✅
- Edge 90+ ✅
- Safari 14+ ✅

No internet connection required after first load (all libraries are CDN-linked on first open).

---

## 📦 Libraries Used (loaded via CDN)

| Library | Version | Purpose |
|---------|---------|---------|
| Chart.js | 4.4.1 | Line, doughnut, and bar charts |
| PapaParse | 5.4.1 | CSV parsing |
| SheetJS (xlsx) | 0.18.5 | Excel file reading (`.xlsx`, `.xls`) |
| DM Sans / DM Mono | — | Typography (Google Fonts) |

---

## 🔧 Customising

### Changing KPI formulas
In `index.html`, find `function renderKPIs()` and adjust the calculation logic. The Conversion Rate card is a static placeholder — replace it with any metric you need.

### Adding more regions or categories
The Region and Category dropdowns are rebuilt automatically from your imported file — no code changes needed.

### Adjusting the transaction table
`function renderTx()` controls the Recent Transactions table. Rows are sorted by month descending. Adjust `sorted` if you want a different sort order.

### Styling
All colours are CSS variables defined in `:root` at the top of the `<style>` block — change `--accent`, `--green`, `--red`, `--amber`, etc. to retheme the entire dashboard in one place.

---

## 💡 How the Multi-Year Import Works

When you import a file, every row's `year` column is read and rows are grouped into a per-year store (`YEAR_DATA`). Switching the Year dropdown calls `filteredTx()` which pulls only from `YEAR_DATA[activeYear]`. The Monthly Revenue Trend chart compares `YEAR_DATA[activeYear]` against `YEAR_DATA[activeYear - 1]` — so if both years are in your file, the comparison uses real data; if the prior year is missing, it falls back to an 88% estimate shown as a dashed line labelled `"(est.)"`.

---

*Built with vanilla HTML, CSS, and JavaScript — no frameworks, no build step, no backend.*
