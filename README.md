# Flight Price Prediction – EDA & Feature Engineering

Exploratory analysis and feature engineering on 10,683 Indian domestic flight tickets (journeys 1 Mar – 27 Jun 2019).
The notebook cleans the raw booking data, parses dates/times/durations, encodes categories and exports a model-ready table.
No model is trained yet; the output is the input for a price-prediction model.

## Project Files

| File | Description |
|---|---|
| `EDA-and-FE_ Flight_Price_Prediction.ipynb` | Notebook: profiling, cleaning, feature engineering, export |
| `flight_price.xlsx` | Raw data: 10,683 rows × 11 columns |
| `flight_price_model_training_data.xlsx` | Processed output: 10,681 rows × 33 numeric columns |
| `README.md` | Project documentation |

## Dataset

| Column (raw) | Description |
|---|---|
| `Airline` | Carrier label (12 labels, 9 carriers; includes Premium/Business variants) |
| `Date_of_Journey` | Travel date, `dd/mm/yyyy` (40 distinct dates) |
| `Source` / `Destination` | Departure city (5) / arrival city (6) |
| `Route` | Airport-code path, e.g. `BLR → DEL` (128 distinct) |
| `Dep_Time` / `Arrival_Time` | `HH:MM`; arrival may carry a next-day date, e.g. `01:10 22 Mar` |
| `Duration` | Text such as `2h 50m` |
| `Total_Stops` | `non-stop` to `4 stops` |
| `Additional_Info` | Extras such as meal/baggage notes (10 labels) |
| `Price` | Ticket price, target variable (currency not stated) |

Processed file: 10 numeric features (`Total_Stops`, `Price`, `Date`, `Month`, `Year`, `Dep_hour`, `Dep_min`, `Arrival_hour`, `Arrival_min`, `Duration_hours`) + 23 one-hot columns (12 airline, 5 source, 6 destination).

## Approach

1. **Understand** – load data, check shape, dtypes, nulls and `Price` summary.
2. **Clean** – split date into day/month/year; split times into hour/minute; extract hours from `Duration`; map `Total_Stops` to 0–4.
3. **Fix gaps** – fill 1 missing `Total_Stops` with the mode (1 stop); drop 1 invalid row (`Duration` = `5m`); drop `Route` and `Additional_Info`.
4. **Encode** – one-hot encode `Airline`, `Source`, `Destination` with scikit-learn.
5. **Export & report** – save the training table to Excel; insights below were recomputed from the raw file.

## Key Insights

| Finding | Value |
|---|---|
| Average / median price | 9,087 / 8,372 (min 1,759, max 79,512; right-skewed, skew 1.81) |
| Busiest route | Delhi → Cochin: 4,537 flights (42.5%), avg 10,539 |
| Largest carrier | Jet Airways: 3,849 flights (36.0%), avg 11,644 |
| Cheapest carriers | SpiceJet 4,338 · Air Asia 5,590 · IndiGo 5,674 · GoAir 5,861 |
| Premium cabin | Jet Airways Business: 6 flights, avg 58,359 |
| Stops | Non-stop avg 5,025 (32.7% of flights) vs 1 stop 10,594 vs 2 stops 12,716 |
| Month | Apr avg 5,771 vs Mar 10,673, May 9,127, Jun 8,829 |
| Duration | ≤3 h avg 4,942; 6–12 h avg 11,201; above 12 h prices flatten (11.4k–11.8k) |
| Departure time | Little effect: bucket averages stay within 8,842–9,218 |
| Baggage | "No check-in baggage included" (320 flights) avg 3,645 |

- Price correlates with stops (r = 0.60) and duration in minutes (r = 0.51).
- 94 flights (0.88%) exceed the IQR upper fence of 23,017; most are Jet Airways (62).

## Tech Stack

- **Language:** Python 3.11 (Jupyter notebook)
- **Libraries:** pandas, NumPy, scikit-learn (`OneHotEncoder`), matplotlib, seaborn (imported, unused), openpyxl (Excel I/O)
- **Techniques:** data profiling, string parsing, missing-value imputation, one-hot encoding

## How to Use

1. Keep the notebook and both `.xlsx` files in one folder (the notebook reads `flight_price.xlsx` by relative path).
2. Install dependencies: `pip install pandas numpy matplotlib seaborn scikit-learn openpyxl jupyter`
3. Launch `jupyter notebook` and open `EDA-and-FE_ Flight_Price_Prediction.ipynb`.
4. Run all cells. This overwrites `flight_price_model_training_data.xlsx` (and reproduces the alignment issue above).
