# Armada Supply Chain Capstone

**CMU 36-490 Capstone Project** — Carnegie Mellon University  
**Industry Partner:** Armada Supply Chain Solutions

---

## Overview

This project analyzes carrier acceptance behavior for freight loads in Armada's supply chain network. Using approximately **460,000 load records** spanning March 2024 through February 2025, the team built predictive models and conducted causal/counterfactual analyses to understand why primary carriers accept or reject loads, and to quantify the financial and operational impact of non-primary (waterfall) carrier usage.

The core prediction task is: **will the tendered primary carrier accept a load?** (binary outcome: `carrier_accepted = 1` for Primary, `0` for non-primary/waterfall).

---

## Team

| Name | Role |
|---|---|
| Yuhao Ren | Exploratory Data Analysis (R) & Predictive Pipeline & Repository (Python) |
| Aidan McIndoe | Key Visualizations (R) |
| Michael Zheng | Predictive Pipeline (Python) |


---

## Repository Structure

```
.
├── Code/
│   ├── Armada EDA Code.Rmd                    # Exploratory Data Analysis (R)
│   ├── Important Visualizations.Rmd           # Key visualizations (R)
│   ├── CMU_Armada_Group_Causal_Analysis.ipynb # Predictive & causal analysis (Python)
│   └── CMU_Armada_Group_Predictive_Pipeline   # Full predictive pipeline (Python/Jupyter)
├── 36-490 Armada Final Presentation.pptx      # Final project presentation
├── 36-490 Armada Poster.pptx                  # Project poster
└── 36-490 Armada Report.docx                  # Full written report
```

---

## Data

The dataset (`CMU_ARMADA_LOADS_202403-202502`) contains freight load records from Armada's Transportation Management System (TMS), split across five Excel/CSV files. Key fields include:

| Category | Fields |
|---|---|
| Load identifiers | `LOAD_ID`, `LANE_ID` |
| Origin / Destination | `ORIGINID`, `DESTINATIONID`, `ORIGIN_REGION`, `DESTINATION_REGION`, origin/destination state |
| Pricing | `CONTRACT_LINEHAUL`, `SPOT_LINEHAUL`, `PAID_LINEHAUL`, `NAP_LINEHAUL` |
| Load characteristics | `MILEAGE`, `WEIGHT`, `CUBE`, `PIECES`, `QUANTITY`, `TRANSIT_TIME`, `NUMBER_OF_STOPS`, `TEMPERATURE_ZONE` |
| Timing | `PICK_DUE_DATE`, `PICK_ACTUAL_DATE`, `PICK_APPT_DATE`, `DROP_DUE_DATE`, `DROP_ACTUAL_DATE`, `DROP_APPT_DATE`, `LOAD_LEAD_TIME` |
| Carrier | `CARRIER_SKEY`, `CARRIER_CLASS_CD` |
| Outcome | `AWARD_TYPE` (Primary, Waterfall #2–6, etc.) |

> **Note:** March and April data were excluded from most analyses due to known seasonal data bias. The final working dataset contains ~384,873 records with a PRIMARY acceptance rate of ~90.6%.

**The data files are not included in this repository** (proprietary). To run the code, place the five CSV files in a folder and update the file paths at the top of each script.

---

## Methods

### 1. Exploratory Data Analysis (`Armada EDA Code.Rmd`)

- Monthly and weekly load volume trends
- Top origin and destination states by load volume
- Pickup and drop delay distributions (clipped to 1st–99th percentile)
- Transit time distributions
- Carrier acceptance rates by lane and region
- Mixed-effects models (`lme4`) for delay analysis

**R libraries:** `tidyverse`, `ggplot2`, `readxl`, `janitor`, `lubridate`, `lme4`, `DHARMa`, `broom.mixed`, `arrow`, `scales`

---

### 2. Key Visualizations (`Important Visualizations.Rmd`)

- Destination region distribution (violin, histogram, density)
- Lane-level load distribution
- Tender acceptance types (`award_type`) bar charts
- Primary vs. non-primary acceptance by temperature requirement, spot rate, mileage, and lead time

**R libraries:** `tidyverse`, `readxl`, `janitor`, `lubridate`, `forcats`, `arrow`

---

### 3. Predictive & Causal Analysis (`CMU_Armada_Group_Causal_Analysis.ipynb`)

**Feature Engineering**
- Price premium percentage (`premium_pct = (PAID_LINEHAUL - SPOT_LINEHAUL) / SPOT_LINEHAUL`)
- Origin region aggregates: total loads, unique destination regions, active carriers, carrier density
- Destination region aggregates: total loads, rare-destination indicator
- Temporal features: month, day of week, quarter

**Predictive Models**
| Model | Library |
|---|---|
| Logistic Regression | `scikit-learn` |
| Gradient Boosting Classifier | `scikit-learn` |
| LightGBM | `lightgbm` |

Models were evaluated using ROC-AUC, accuracy, and classification reports on a held-out test set.

**Causal / Counterfactual Analysis**
- Constructed a counterfactual dataset to simulate changes in pricing or market conditions
- Used the calibrated LightGBM model to estimate causal effects of price premium on acceptance
- Ran full-market simulations to quantify expected savings or cost implications of pricing strategies

**Python libraries:** `pandas`, `numpy`, `scikit-learn`, `lightgbm`, `matplotlib`, `seaborn`

---

## Setup & Usage

### R scripts

1. Install required R packages:
   ```r
   install.packages(c("tidyverse", "readxl", "janitor", "arrow",
                      "lubridate", "lme4", "DHARMa", "broom.mixed",
                      "scales", "forcats"))
   ```
2. Place the five Armada XLSX data files in the same directory as the `.Rmd` file.
3. Open the `.Rmd` file in RStudio and click **Knit**, or run chunks interactively.

### Python notebook

1. Install required Python packages:
   ```bash
   pip install pandas numpy scikit-learn lightgbm matplotlib seaborn
   ```
2. The notebook was developed on **Google Colab** with data stored on **Google Drive**. Update the `base` file path variable near the top of the notebook to point to your local data directory:
   ```python
   base = "/path/to/your/data"
   ```
3. Open the notebook in Jupyter or Google Colab and run all cells in order.

---

## Key Results

- The trained LightGBM model achieved strong predictive performance for primary carrier acceptance.
- Price premium over the spot rate is a significant predictor of acceptance probability.
- Counterfactual simulations revealed actionable pricing strategies to increase acceptance rates and reduce operational costs from fallback carrier usage.

For full results and discussion, see the written report (`36-490 Armada Report.docx`) and the final presentation (`36-490 Armada Final Presentation.pptx`).

---

## License

This project was created for academic purposes as part of the CMU 36-490 capstone course. The underlying data is proprietary to Armada Supply Chain Solutions and is not included in this repository.
