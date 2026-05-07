# ☀️ Solar PV Database & SEDA Calculator

> A professional Solar Photovoltaic monitoring and calculation system built with Python and Streamlit. Combines **field measurements** with **SEDA-based theoretical calculations** to compare Real Operating Condition (ROC) performance against predicted values.

---

## 📸 System Overview

```
┌─────────────────────────────────────────────────────────┐
│              SOLAR PV DATABASE & CALCULATOR             │
│        Field Measured  vs  SEDA Calculated (ROC)        │
├──────────────┬──────────────┬──────────────┬────────────┤
│  📊 Dashboard │ 🔬 Field Meas │ 🧮 Calculator │ 📐 Compare │
└──────────────┴──────────────┴──────────────┴────────────┘
```

---

## 🚀 Features

| Module | Description |
|--------|-------------|
| 📊 **Dashboard** | Live KPIs, time-series charts, Measured vs Calculated power overlay |
| 🔬 **Field Measurement** | Log field readings: Irradiance, PV Temp, Voc, Vmp, Isc, Imp, Power |
| 🧮 **SEDA Calculator** | Full ROC output estimation using SEDA Equations 5.4 – 5.14 |
| 📐 **Comparison Analysis** | % deviation table, bar charts, parity plot (Calculated vs Measured) |
| 📤 **Import / Export** | Bulk import via CSV/Excel, export results for reporting |
| 📈 **Analytics** | Scatter plots, I-V & P-V curves, temperature effect analysis |

---

## 📐 Parameters Tracked

### Environmental
| Parameter | Unit | Description |
|-----------|------|-------------|
| Solar Irradiance | W/m² | In-plane solar irradiance at site |
| PV Temperature | °C | PV module/cell operating temperature |
| Ambient Temperature | °C | Surrounding air temperature |

### Electrical
| Parameter | Symbol | Unit |
|-----------|--------|------|
| Open Circuit Voltage | Voc | V |
| Voltage at Maximum Power | Vmp | V |
| Short Circuit Current | Isc | A |
| Current at Maximum Power | Imp | A |
| Maximum Power Output | P_max | W |

---

## 📚 SEDA Calculation Reference

Based on: **Fundamentals of Solar Photovoltaics Technology** (SEDA Malaysia)

### Equations Used

#### Power Output [Eq 5.7 & 5.8]
```
P_max = P_maxSTC × f_mm × f_degrad × f_temp_p × f_g × f_clean × f_unshade

f_degrad = f_LID × f_age
```

#### Irradiance Factor [Eq 5.9]
```
f_g = G_i / 1,000
```

#### Temperature Factor for Power [Eq 5.10]
```
f_temp_p = 1 + (γ_Pmax / 100%) × (T_mod − T_STC)
```

#### Current Output [Eq 5.11 & 5.12]
```
I_x = I_xSTC × f_temp_i × f_g × f_clean × f_unshade

f_temp_i = 1 + (α_Ix / 100%) × (T_mod − T_STC)
```

#### Voltage Output [Eq 5.13 & 5.14]
```
V_x = V_xSTC × f_temp_v

f_temp_v = 1 + (β_Vx / 100%) × (T_mod − T_STC)
```

#### PV Device Temperature Models

**NOCT Model [Eq 5.4]**
```
T_x = T_amb + [ G_i × (NOCT − 20°C) / 800 Wm⁻² ]
```

**Malaysian Climate Empirical Model [Eq 5.6]** *(Zainuddin, H., 2014)*
```
T_x = −15.76 + 0.02 × G_i + 1.64 × T_amb
```

### Adjustment Factors Summary

| Factor | Symbol | Formula / Description |
|--------|--------|-----------------------|
| Irradiance | f_g | G / 1000 |
| LID Degradation | f_LID | From datasheet (e.g. 0.975) |
| Aging | f_age | 1 − [(LID% + 0.5% × years) / 100] |
| Combined Degradation | f_degrad | f_LID × f_age |
| Temperature (Power) | f_temp_p | 1 + (γ/100%) × (T_mod − 25) |
| Temperature (Current) | f_temp_i | 1 + (α/100%) × (T_mod − 25) |
| Temperature (Voltage) | f_temp_v | 1 + (β/100%) × (T_mod − 25) |
| Dirt / Soiling | f_clean | (100 − dirt%) / 100 |
| Shading | f_unshade | (100 − shade%) / 100 |
| Mismatch | f_mm | From datasheet (typically 0.97–1.0) |

### Array Calculations

For a PV array with N_p strings in parallel and N_s modules in series:

```
P_A STC   = N_p × N_s × P_maxSTC
I_A Pmax  = N_p × I_PmaxSTC × f_temp_i × f_g × f_clean × f_unshade
V_A Pmax  = N_s × V_PmaxSTC × f_temp_v
```

---

## 🛠️ Local Setup

### Prerequisites
- Python 3.9 or higher
- pip

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/solar-pv-seda.git
cd solar-pv-seda

# 2. Install dependencies
pip install -r requirements.txt

# 3. Run the application
streamlit run app.py
```

The app will open at `http://localhost:8501`

---

## ☁️ Deploy to Streamlit Cloud

1. **Push to GitHub**
   ```bash
   git init
   git add .
   git commit -m "Initial commit — Solar PV SEDA Calculator"
   git remote add origin https://github.com/YOUR_USERNAME/solar-pv-seda.git
   git push -u origin main
   ```

2. **Deploy on Streamlit Cloud**
   - Go to [share.streamlit.io](https://share.streamlit.io)
   - Sign in with your GitHub account
   - Click **New app**
   - Select your repository
   - Set **Main file path** to `app.py`
   - Click **Deploy!**

> ⚠️ **Note on Data Persistence:** The SQLite database (`solar_pv_v3.db`) is stored in Streamlit Cloud's ephemeral filesystem. Data will reset on redeployment. For persistent storage, see the [Persistent Storage](#-persistent-storage-optional) section below.

---

## 📁 Project Structure

```
solar-pv-seda/
│
├── app.py                      # Main Streamlit application
├── requirements.txt            # Python dependencies
├── README.md                   # This file
│
└── .streamlit/
    └── config.toml             # Theme & server configuration
```

---

## 📊 CSV Import Format

Download the template from the **Import / Export** page, or use this structure:

| Column | Type | Example | Required |
|--------|------|---------|----------|
| `recorded_at` | datetime | `2024-01-01 08:00:00` | ✅ |
| `site_name` | text | `Site A` | ✅ |
| `irradiance` | float | `800.0` | ✅ |
| `pv_temp` | float | `45.0` | ✅ |
| `ambient_temp` | float | `30.0` | ✅ |
| `voc_measured` | float | `40.5` | ✅ |
| `vmp_measured` | float | `33.2` | ✅ |
| `isc_measured` | float | `10.05` | ✅ |
| `imp_measured` | float | `9.49` | ✅ |
| `power_measured` | float | `315.0` | ✅ |
| `notes` | text | `Clear sky` | ❌ |

---

## 🗄️ Database Schema

The app uses **SQLite** with three tables:

```sql
-- Field measurement records
measured_data (id, recorded_at, site_name, irradiance, pv_temp,
               ambient_temp, voc_measured, vmp_measured, isc_measured,
               imp_measured, power_measured, notes, created_at)

-- SEDA calculation results
calc_results  (id, measured_id, calc_at, site_name,
               [datasheet params], [ROC inputs], [factors],
               [calculated outputs], [measured outputs for comparison])

-- Site registry
sites         (id, name, location, capacity, created_at)
```

---

## 💾 Persistent Storage (Optional)

To use **PostgreSQL** (e.g. [Supabase](https://supabase.com) — free tier available) instead of SQLite:

1. Replace `get_conn()` in `app.py`:
   ```python
   import psycopg2
   import streamlit as st

   def get_conn():
       return psycopg2.connect(st.secrets["DATABASE_URL"])
   ```

2. Add your database URL in Streamlit Cloud:
   - Go to your app → **Settings → Secrets**
   - Add:
     ```toml
     DATABASE_URL = "postgresql://user:password@host:5432/dbname"
     ```

3. Add `psycopg2-binary` to `requirements.txt`

---

## 📦 Dependencies

| Package | Version | Purpose |
|---------|---------|---------|
| `streamlit` | ≥ 1.32.0 | Web application framework |
| `pandas` | ≥ 2.0.0 | Data manipulation |
| `numpy` | ≥ 1.24.0 | Numerical calculations |
| `plotly` | ≥ 5.18.0 | Interactive charts & plots |
| `openpyxl` | ≥ 3.1.0 | Excel file support |
| `statsmodels` | ≥ 0.14.0 | Trendline (OLS/LOWESS) in scatter plots |

---

## 📖 How to Use — Step by Step

### Step 1 — Add a Site
Navigate to **🔬 Field Measurement** → scroll down to **Manage Sites** → enter site name, location, and capacity → click **Add Site**.

### Step 2 — Log Field Measurements
In **🔬 Field Measurement**, fill in the date, time, site, and all measured electrical and environmental values → click **Save Measurement**.

### Step 3 — Run SEDA Calculation
In **🧮 SEDA Calculator**:
- Enter the module datasheet values (STC parameters, temperature coefficients)
- Enter the ROC conditions (irradiance, module temperature, dirt, shading, aging)
- Optionally enter your field measured values for instant comparison
- Click **CALCULATE NOW**

### Step 4 — Compare Results
In **📐 Comparison Analysis**, view:
- Side-by-side table of calculated vs measured values
- % deviation bar charts (green = within ±5%, red = exceeds ±5%)
- Parity plot (points on the gold line = perfect agreement)

### Step 5 — Export Reports
In **📤 Import / Export**, download your field data and calculation results as CSV files for further analysis or reporting.

---

## 📝 License

This project is for academic and professional use in the field of solar photovoltaics engineering.

---

## 🙏 Acknowledgements

- **SEDA Malaysia** — *Fundamentals of Solar Photovoltaics Technology*
- **Zainuddin, H. (2014)** — Malaysian climate PV temperature empirical model [Eq 5.6]
- Built with [Streamlit](https://streamlit.io) · [Plotly](https://plotly.com) · [SQLite](https://sqlite.org)
