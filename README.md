# ☀️ Solar PV Database, SEDA Computation & ANN Prediction

> A comprehensive Solar Photovoltaic monitoring and analysis system built with **Python** and **Streamlit**.  
> Combines **field measurements**, **SEDA-based theoretical computation**, and an **ANN machine learning prediction model** for bifacial PV power output.

---

## 🏗️ System Architecture

```
┌─────────────┐     deploy code      ┌─────────────────────┐
│   GitHub    │ ──────────────────►  │   Streamlit Cloud   │
│  (app.py)   │                      │   (runs the app)    │
└─────────────┘                      └──────────┬──────────┘
                                                │
                                       save / load data
                                                │
                                     ┌──────────▼──────────┐
                                     │      Supabase       │
                                     │   (PostgreSQL DB)   │
                                     │  permanent storage  │
                                     └─────────────────────┘
```

| Service | Role | Cost |
|---------|------|------|
| **GitHub** | Stores your code | Free |
| **Streamlit Cloud** | Runs the app on the internet | Free |
| **Supabase** | Stores all data permanently | Free |

---

## 📸 System Modules

| Module | Description |
|--------|-------------|
| 📊 **Dashboard** | Live KPIs, time-series charts, Measured vs Computed power overlay |
| 🔬 **Field Measurement** | Log site readings — Irradiance, PV Temp, Voc, Vmp, Isc, Imp, Power |
| 🧮 **SEDA Computation** | Full ROC output estimation using SEDA Equations 5.4 – 5.14 |
| 📐 **Comparison Analysis** | % deviation table, deviation bar charts, parity plot |
| 📤 **Import / Export** | Bulk CSV/Excel import, formatted Excel report export |
| 📈 **Analytics** | Scatter plots, I-V & P-V curves, temperature analysis |
| 🤖 **ANN Prediction** | Train, predict, and validate using Artificial Neural Network |

---

## 📐 Parameters Tracked

### Environmental
| Parameter | Unit |
|-----------|------|
| Solar Irradiance | W/m² |
| PV Temperature | °C |
| Ambient Temperature | °C |

### Electrical (Field Measured)
| Parameter | Symbol | Unit |
|-----------|--------|------|
| Open Circuit Voltage | Voc | V |
| Voltage at Maximum Power | Vmp | V |
| Short Circuit Current | Isc | A |
| Current at Maximum Power | Imp | A |
| Maximum Power Output | P_max | W |

---

## 📚 SEDA Computation Reference

Based on: **Fundamentals of Solar Photovoltaics Technology (SEDA Malaysia)**

### Equations Used

| Equation | Formula |
|----------|---------|
| **Eq 5.7** | P_max = P_STC x f_mm x f_degrad x f_temp_p x f_g x f_clean x f_unshade |
| **Eq 5.8** | f_degrad = f_LID x f_age |
| **Eq 5.9** | f_g = G / 1,000 |
| **Eq 5.10** | f_temp_p = 1 + (gamma/100%) x (T_mod - T_STC) |
| **Eq 5.11** | I_x = I_xSTC x f_temp_i x f_g x f_clean x f_unshade |
| **Eq 5.12** | f_temp_i = 1 + (alpha/100%) x (T_mod - T_STC) |
| **Eq 5.13** | V_x = V_xSTC x f_temp_v |
| **Eq 5.14** | f_temp_v = 1 + (beta/100%) x (T_mod - T_STC) |
| **Eq 5.4** | T_x = T_amb + [G x (NOCT - 20) / 800] |
| **Eq 5.6** | T_x = -15.76 + 0.02G + 1.64 x T_amb (Malaysian climate model) |

### Error Formula

```
Error (%) = (P_measured - P_computed) / P_computed x 100%
```

- Positive error: Measured power is higher than computed
- Negative error: Measured power is lower than computed
- Within +-5%: Acceptable tolerance

---

## 🤖 ANN Prediction Module

The ANN module uses field measurement data collected in the app to train a neural network that predicts bifacial PV power output.

### Workflow

```
Field Data (DB)  ->  Train ANN  ->  Predict  ->  Validate  ->  Export Results
```

### Input & Output

| Type | Parameter | Source |
|------|-----------|--------|
| Input (X) | Solar Irradiance | irradiance column |
| Input (X) | PV Temperature | pv_temp column |
| Input (X) | Ambient Temperature | ambient_temp column |
| Output (Y) | Power Output | power_measured column |

### ANN Configuration

| Setting | Options |
|---------|---------|
| Hidden Layers | 1, 2, or 3 |
| Neurons per layer | 4 to 256 (configurable) |
| Activation function | relu / tanh / sigmoid |
| Optimizer | Adam / SGD / RMSProp |
| Early stopping | Configurable patience |
| Train/test split | Configurable (default 80/20) |

### Validation Metrics

| Metric | Target |
|--------|--------|
| RMSE | Lower is better |
| R² | >= 0.95 = Excellent, >= 0.90 = Good |
| MAE | Lower is better |
| MAPE | < 5% = Excellent |

### ANN Output

- Predicted vs Actual power chart
- Parity plot with R² score
- Training loss curve per epoch
- Export results to Excel for FYP report

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

# 3. Create local secrets file
mkdir -p .streamlit
echo 'DATABASE_URL = "postgresql://postgres:[PASSWORD]@db.xxxx.supabase.co:5432/postgres"' > .streamlit/secrets.toml

# 4. Run the app
streamlit run app.py
```

---

## ☁️ Deployment Guide

### Step 1 — Set up Supabase (Persistent Database)

1. Go to https://supabase.com and sign up free
2. Click New Project, set a name and password, then Create
3. Go to Settings > Database > Connection string > Session Pooler
4. Copy the connection string (looks like):
   ```
   postgresql://postgres.xxxx:[PASSWORD]@aws-0-xx.pooler.supabase.com:5432/postgres
   ```

### Step 2 — Push to GitHub

```bash
git init
git add app.py requirements.txt README.md .streamlit/config.toml .gitignore
git commit -m "Solar PV SEDA Computation + ANN Prediction"
git remote add origin https://github.com/YOUR_USERNAME/solar-pv-seda.git
git push -u origin main
```

### Step 3 — Deploy on Streamlit Cloud

1. Go to https://share.streamlit.io
2. Sign in with GitHub and click New app
3. Select your repository and set Main file path to app.py
4. Click Advanced settings > Secrets and paste:

```toml
DATABASE_URL = "postgresql://postgres.xxxx:[PASSWORD]@aws-0-xx.pooler.supabase.com:5432/postgres"
```

5. Click Deploy!

Data is permanently saved in Supabase and will never disappear when Streamlit restarts.

---

## 📁 Project Structure

```
solar-pv-seda/
│
├── app.py                  # Main Streamlit application (all 7 modules)
├── requirements.txt        # Python dependencies
├── README.md               # This file
├── .gitignore              # Protects secrets from GitHub
│
└── .streamlit/
    ├── config.toml         # Theme and server configuration
    └── secrets.toml        # LOCAL ONLY — never push to GitHub
```

---

## 🔒 .gitignore

```
# Secrets — never push to GitHub
.streamlit/secrets.toml

# Old SQLite database
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
| recorded_at | datetime | 2024-01-01 09:00:00 | Yes |
| site_name | text | QB Building | Yes |
| irradiance | float | 800.0 | Yes |
| pv_temp | float | 45.0 | Yes |
| ambient_temp | float | 30.0 | Yes |
| voc_measured | float | 40.5 | Yes |
| vmp_measured | float | 33.2 | Yes |
| isc_measured | float | 10.05 | Yes |
| imp_measured | float | 9.49 | Yes |
| power_measured | float | 315.0 | Yes |
| notes | text | Clear sky | No |

Note: Enter the exact time you measured. The system stores it as-is without any timezone conversion.

---

## 📦 Dependencies

| Package | Purpose |
|---------|---------|
| streamlit >= 1.32.0 | Web application framework |
| pandas >= 2.0.0 | Data manipulation |
| numpy >= 1.24.0 | Numerical calculations |
| plotly >= 5.18.0 | Interactive charts |
| psycopg2-binary >= 2.9.0 | PostgreSQL connection (Supabase) |
| openpyxl >= 3.1.0 | Excel file support |
| statsmodels >= 0.14.0 | Trendlines in scatter plots |
| scikit-learn >= 1.3.0 | Data preprocessing and metrics for ANN |
| tensorflow >= 2.13.0 | ANN model training and prediction |

---

## 📖 How to Use — Step by Step

Step 1 — Go to Field Measurement, add a site, and log your field readings

Step 2 — Go to SEDA Computation, enter module datasheet values and ROC conditions, then click COMPUTE NOW

Step 3 — Go to Comparison Analysis to view the percentage deviation between computed and measured values

Step 4 — Go to ANN Prediction, train the model using your collected data, predict new values, and export the results

Step 5 — Go to Import / Export to generate a formatted Excel report for documentation or FYP submission

---

## 🙏 Acknowledgements

- SEDA Malaysia — Fundamentals of Solar Photovoltaics Technology
- Zainuddin, H. (2014) — Malaysian climate PV temperature empirical model (Eq 5.6)
- Built with Streamlit, Supabase, TensorFlow, and Plotly
