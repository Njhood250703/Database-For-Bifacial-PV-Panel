# ☀️ Solar PV Database & SEDA Calculator

> A professional Solar Photovoltaic monitoring and calculation system built with Python and Streamlit. Combines **field measurements** with **SEDA-based theoretical calculations** to compare Real Operating Condition (ROC) performance against predicted values.
>
> 💾 Data is stored permanently in **Supabase (PostgreSQL)** — data will never disappear even when Streamlit restarts.

---

## 🏗️ System Architecture

```
┌─────────────┐     deploy code      ┌─────────────────┐
│   GitHub    │ ──────────────────►  │ Streamlit Cloud │
│  (app.py)   │                      │  (runs the app) │
└─────────────┘                      └────────┬────────┘
                                              │
                                     save/load data
                                              │
                                     ┌────────▼────────┐
                                     │    Supabase     │
                                     │  (PostgreSQL)   │
                                     │  permanent data │
                                     └─────────────────┘
```

| Service | Role | Cost |
|---------|------|------|
| **GitHub** | Stores your `app.py` code | Free |
| **Streamlit Cloud** | Runs the app on the internet | Free |
| **Supabase** | Stores all data permanently | Free |

---

## 📸 System Overview

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
| Parameter | Unit |
|-----------|------|
| Solar Irradiance | W/m² |
| PV Temperature | °C |
| Ambient Temperature | °C |

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

| Equation | Formula | Description |
|----------|---------|-------------|
| **Eq 5.7** | P_max = P_maxSTC × f_mm × f_degrad × f_temp_p × f_g × f_clean × f_unshade | Power output |
| **Eq 5.8** | f_degrad = f_LID × f_age | Degradation factor |
| **Eq 5.9** | f_g = G / 1,000 | Irradiance factor |
| **Eq 5.10** | f_temp_p = 1 + (γ/100%) × (T_mod − T_STC) | Temperature factor (Power) |
| **Eq 5.11** | I_x = I_xSTC × f_temp_i × f_g × f_clean × f_unshade | Current output |
| **Eq 5.12** | f_temp_i = 1 + (α/100%) × (T_mod − T_STC) | Temperature factor (Current) |
| **Eq 5.13** | V_x = V_xSTC × f_temp_v | Voltage output |
| **Eq 5.14** | f_temp_v = 1 + (β/100%) × (T_mod − T_STC) | Temperature factor (Voltage) |
| **Eq 5.4** | T_x = T_amb + [G × (NOCT − 20) / 800] | NOCT temperature model |
| **Eq 5.6** | T_x = −15.76 + 0.02G + 1.64 × T_amb | Malaysian climate model |

---

## 🛠️ Local Setup

```bash
# 1. Clone the repository
git clone https://github.com/YOUR_USERNAME/solar-pv-seda.git
cd solar-pv-seda

# 2. Install dependencies
pip install -r requirements.txt

# 3. Add your Supabase secret (for local)
mkdir -p .streamlit
echo '[secrets]' > .streamlit/secrets.toml
echo 'DATABASE_URL = "postgresql://postgres:[PASSWORD]@db.xxxx.supabase.co:5432/postgres"' >> .streamlit/secrets.toml

# 4. Run the app
streamlit run app.py
```

---

## ☁️ Full Deployment Guide

### Step 1 — Set up Supabase (Persistent Database)

1. Go to [supabase.com](https://supabase.com) → Sign up free
2. Click **New Project** → enter a name and password → click **Create**
3. Wait for the project to be ready (~1 minute)
4. Go to **Settings → Database → Connection string → URI**
5. Copy the connection string:
   ```
   postgresql://postgres:[YOUR-PASSWORD]@db.xxxx.supabase.co:5432/postgres
   ```
   > ⚠️ Replace `[YOUR-PASSWORD]` with the password you set in step 2

### Step 2 — Push Code to GitHub

```bash
git init
git add .
git commit -m "Solar PV SEDA Calculator with Supabase"
git remote add origin https://github.com/YOUR_USERNAME/solar-pv-seda.git
git push -u origin main
```

### Step 3 — Deploy on Streamlit Cloud

1. Go to [share.streamlit.io](https://share.streamlit.io)
2. Sign in with your GitHub account
3. Click **New app**
4. Select your repository and set **Main file path** to `app.py`
5. Click **Advanced settings → Secrets** and add:
   ```toml
   DATABASE_URL = "postgresql://postgres:[YOUR-PASSWORD]@db.xxxx.supabase.co:5432/postgres"
   ```
6. Click **Deploy!**

> ✅ Your data is now saved permanently in Supabase and will **never disappear**.

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
    └── secrets.toml            # ⚠️ LOCAL ONLY — never push to GitHub
```

> ⚠️ **Important:** Never push `secrets.toml` to GitHub. Add `.streamlit/secrets.toml` to your `.gitignore` file.

---

## 🔒 .gitignore (Important!)

Create a `.gitignore` file in your project root to protect your secrets:

```
# Secrets — never push to GitHub
.streamlit/secrets.toml

# Database file (old SQLite — not used anymore)
*.db

# Python cache
__pycache__/
*.pyc
.env
```

---

## 📊 CSV Import Format

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

## 📦 Dependencies

| Package | Purpose |
|---------|---------|
| `streamlit` | Web application framework |
| `pandas` | Data manipulation |
| `numpy` | Numerical calculations |
| `plotly` | Interactive charts |
| `psycopg2-binary` | PostgreSQL connection (Supabase) |
| `openpyxl` | Excel file support |
| `statsmodels` | Trendlines in scatter plots |

---

## 📖 How to Use

1. **Add a Site** → 🔬 Field Measurement → Manage Sites → enter name & location
2. **Log Field Data** → 🔬 Field Measurement → fill in all measured values → Save
3. **Run SEDA Computation** → 🧮 SEDA Calculator → enter datasheet + ROC values → Calculate Now
4. **Compare Results** → 📐 Comparison Analysis → view % deviation and parity plot
5. **Export Reports** → 📤 Import / Export → download CSV files

---

## 🙏 Acknowledgements

- **SEDA Malaysia** — *Fundamentals of Solar Photovoltaics Technology*
- **Zainuddin, H. (2014)** — Malaysian climate PV temperature empirical model
- Built with [Streamlit](https://streamlit.io) · [Supabase](https://supabase.com) · [Plotly](https://plotly.com)
