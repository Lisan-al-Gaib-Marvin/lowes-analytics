# Lowe's Companies (NYSE: LOW) — Full-Stack Data Science Project

**An end-to-end analytics project analyzing Lowe's Companies across financial performance, customer sentiment, supply chain operations, and sustainability — built with MySQL, Python, Claude API, Tableau, and Power BI.**

> Capstone-style project for MS in Data Science & Business Analytics at UNC Charlotte (2026)

---

## The Problem

Lowe's is a Fortune 50 home improvement retailer headquartered in Mooresville, NC, with $86.3 billion in annual revenue and 1,759 stores nationwide. Despite its scale, the company faces declining operating margins (from 13.4% to 11.8% over three years) and uneven customer satisfaction across its home state of North Carolina.

**This project asks:** What drives customer engagement at Lowe's NC stores, how do financial and operational metrics interact, and where are the opportunities for improvement?

---

## What I Built

This is not a single notebook — it is a complete data pipeline from raw SEC filings to an interactive executive dashboard, covering four business domains:

| Domain | Data Source | What I Analyzed |
|--------|-----------|-----------------|
| **Financial Performance** | SEC EDGAR (10-K, 8-K filings) | Revenue, margins, EPS, inventory turnover across 3 fiscal years |
| **Customer Sentiment** | Google Maps (118 NC stores) | Store ratings, review volume, geographic patterns |
| **Supply Chain / Operations** | SEC filings + derived KPIs | Revenue per store, revenue per sqft, inventory turnover, capex intensity |
| **Sustainability** | Lowe's Corporate Responsibility Reports | Scope 1/2/3 emissions, renewable energy adoption, waste diversion |

---

## Architecture

```
DATA SOURCES              MYSQL                    PYTHON / JUPYTER           REPORTING
─────────────             ──────                   ────────────────           ─────────
SEC EDGAR          ──▶   financials          ──▶   EDA & Visualization  ──▶  Tableau Dashboard
Google Maps        ──▶   reviews             ──▶   Linear Regression    ──▶  Power BI Report
CSR Reports        ──▶   sustainability      ──▶   K-Means Clustering        
                                              ──▶   Claude API (Sonnet 4.6)
                                                    ├─ Feature Selection
                                                    └─ Executive Narrative
```

---

## Key Findings

### Financial Trends (FY2023–FY2025)
- Revenue held steady at ~$84–86B, but **operating margin declined from 13.4% to 11.8%** — a compression of 160 basis points driven by acquisition costs and SGA growth
- Diluted EPS fell from $13.20 to $11.85 despite stable top-line revenue
- Inventory turnover recovered to 3.3x after dipping to 3.2x, suggesting improving supply chain discipline

### Customer Sentiment (118 NC Stores)
- Average Google Maps rating across NC: **4.13 / 5.0** with a tight distribution (3.8–4.6)
- Charlotte Metro leads in both rating (4.20) and review volume, consistent with higher urban foot traffic
- Mountains/Asheville region underperforms at 3.98 average rating — the only region below 4.0

### Modeling Results
- **Linear Regression (R² = -0.38):** Geographic features alone do not predict review volume well. This is an honest finding — it tells us that store-level engagement depends on factors not captured in public data (store size, staffing, local competition). The negative R² is itself informative and guided recommendations for future data collection.
- **K-Means Clustering (K=3, Silhouette=0.38):** Segmented stores into High-Traffic Urban, Mid-Market Suburban, and Low-Traffic Rural groups. Cluster separation was driven primarily by geographic position and review volume.

### AI Integration
- Claude API (Sonnet 4.6) reviewed the dataset schema and flagged a **longitude data quality issue** and **target leakage in the volume_tier feature** — catches I incorporated before finalizing the models
- Claude generated the executive summary used in the Tableau dashboard narrative

---

## Dashboard Preview

### Tableau Executive Dashboard
*KPI cards with YoY trends, NC store map by rating tier, quarterly revenue trend, and ratings by region*

![Tableau Dashboard](dashboards/tableau/dashboard_screenshot.png)

### Key Visuals from Analysis

| Financial Trends | Inventory Turnover | Store Ratings by Region |
|:---:|:---:|:---:|
| ![Trends](outputs/financial_trends_4panel.png) | ![Turnover](outputs/inventory_turnover.png) | ![Regions](outputs/rating_by_region.png) |

| K-Means Clustering | Rating Distribution | Revenue & Margin |
|:---:|:---:|:---:|
| ![Clusters](outputs/kmeans_clusters.png) | ![Distribution](outputs/rating_distribution.png) | ![Revenue](outputs/revenue_margin_trend.png) |

---

## Project Structure

```
lowes-analytics/
├── README.md
├── requirements.txt
├── .gitignore
│
├── sql/
│   ├── lowes_db_creation.sql        # Schema: 6 tables (financials, reviews, sustainability, employment, stock)
│   └── cleaning_tables.sql          # Transforms: staging → clean tables with derived KPIs
│
├── scripts/
│   ├── lowes_etl_financials.ipynb   # ETL: SEC filing data → MySQL (upsert logic)
│   └── lowes_etl_sustainability.ipynb # ETL: CSR report data → MySQL
│
├── notebooks/
│   ├── 01_eda_financials.ipynb      # EDA: revenue trends, margin analysis, inventory turnover
│   ├── 02_modeling.ipynb            # Models: linear regression, K-Means clustering, financial trends
│   └── 03_claude_integration.ipynb  # AI: Claude API for feature selection + executive narrative
│
├── data/
│   ├── raw/                         # Source CSVs (financials, reviews, sustainability)
│   └── clean/                       # Cleaned exports for Tableau/Power BI
│
├── outputs/                         # Chart PNGs, Claude API responses
│   ├── financial_trends_4panel.png
│   ├── kmeans_clusters.png
│   ├── rating_by_region.png
│   ├── claude_feature_selection.md
│   └── claude_executive_summary.md
│
├── dashboards/
│   ├── tableau/                     # Tableau workbook + screenshots
│   └── powerbi/                     # Power BI report + screenshots
│
└── app/                             # (Optional) Streamlit interactive app
    └── streamlit_app.py
```

---

## Tools & Technologies

| Layer | Tools |
|-------|-------|
| **Database** | MySQL 8.0 — schema design, staging/clean tables, derived KPIs |
| **ETL** | Python (mysql.connector, csv) — upsert logic, null handling, data validation |
| **Analysis** | Pandas, NumPy, Matplotlib, Seaborn — EDA, correlation analysis |
| **Modeling** | scikit-learn — Linear Regression, K-Means, PCA, StandardScaler, silhouette scoring |
| **AI Integration** | Anthropic Claude API (Sonnet 4.6) — feature selection, executive narrative generation |
| **Dashboards** | Tableau Desktop (LaDataViz KPI extension), Power BI |
| **Data Sources** | SEC EDGAR, Google Maps, Lowe's Corporate Responsibility Reports |

---

## How to Run

### Prerequisites
- Python 3.9+
- MySQL 8.0
- Tableau Desktop (for dashboard)

### Setup
```bash
# Clone the repo
git clone https://github.com/Lisan-al-Gaib-Marvin/lowes-analytics.git
cd lowes-analytics

# Install dependencies
pip install -r requirements.txt

# Set up the database
mysql -u root -p < sql/lowes_db_creation.sql

# Set your API key (for Claude integration notebook)
export ANTHROPIC_API_KEY="your-key-here"
```

### Run Order
1. `sql/lowes_db_creation.sql` — Create database and tables
2. `scripts/lowes_etl_financials.ipynb` — Load financial data
3. `scripts/lowes_etl_sustainability.ipynb` — Load sustainability data
4. Load review CSV into `reviews` table
5. `sql/cleaning_tables.sql` — Create clean tables with derived KPIs
6. `notebooks/01_eda_financials.ipynb` — Exploratory analysis
7. `notebooks/02_modeling.ipynb` — Regression, clustering, trends
8. `notebooks/03_claude_integration.ipynb` — AI-assisted analysis
9. Open Tableau workbook and connect to clean CSVs

---

## What I Learned

This project pushed me to work across the entire data science stack — not just modeling, but database design, ETL pipelines, data quality checks, and communicating results through multiple dashboard tools. A few things that stood out:

- **Data collection is the hardest part.** Building the financial dataset meant reading SEC filings line by line and manually extracting numbers from press releases. Building the review dataset meant systematically querying Google Maps for 118 stores across 93 NC cities. Neither was glamorous, but both taught me more about real-world data work than any classroom assignment.

- **A negative R² is a valid finding.** My linear regression returned R² = -0.38, which means the model performs worse than simply predicting the mean. Rather than hiding this, I used it as evidence that store-level engagement depends on factors beyond public geographic data — and that finding directly informed my recommendation to collect store square footage and transaction volume for future work.

- **AI tools are collaborators, not replacements.** Claude API caught a longitude sign error and target leakage issue in my features that I had missed. It also generated an executive summary that I used as a starting point (not a final draft) for the Tableau dashboard narrative. The value was in the back-and-forth — sending data summaries and getting specific, actionable feedback.

---

## Future Work

- Add individual review text for NLP sentiment analysis (currently only aggregate ratings)
- Incorporate BLS employment data for NC counties to add the workforce/economic domain
- Build a Streamlit app for interactive store comparison
- Expand financial data to include quarterly segments and Home Depot comparison
- Integrate sustainability metrics into the Tableau dashboard as a dedicated story point

---

## Contact

**Marvin Pearson** — MS Data Science & Business Analytics, UNC Charlotte (Class of 2027)
- LinkedIn: [linkedin.com/in/marvin-pearson](https://linkedin.com/in/marvin-pearson)
- GitHub: [github.com/Lisan-al-Gaib-Marvin](https://github.com/Lisan-al-Gaib-Marvin)
- Email: mpears30@charlotte.edu
