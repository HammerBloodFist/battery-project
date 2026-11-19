# Cell-Disjoint Uncertainty Calibration for Early Battery-Life Prediction

Course project for 3000004032 – Advanced AI (Project 20).
Author: Hamza Irfan (262168013)

## Question

Does group-aware conformal calibration, which treats each battery cell as one unit instead of each cycle as an independent sample, give better-calibrated prediction intervals for early-cycle battery-life prediction?

## Data

SNL subset of the BatteryLife dataset (Tan et al., KDD 2025): 52 commercial Li-ion 18650 cells (LFP 9, NCA 22, NMC 21), 3 temperatures (15, 25, 35 °C), 23 charge/discharge protocols. Lifetimes range from 143 to 3,842 cycles.

The dataset is **not included** in this repository because of its size. To run the notebook:

1. Open https://zenodo.org/records/14969822 (use version v4).
2. Download `SNL.zip` and `Life labels.zip`.
3. Unzip both into a folder named `dataset` next to the notebook, so you have `dataset/SNL/` and `dataset/Life labels/`.

Please cite BatteryLife and the original data paper: *Degradation of Commercial Lithium-Ion Cells as a Function of Chemistry and Cycling Conditions*, J. Electrochem. Soc., 2020. Check the license terms on the Zenodo record before reusing the data.

## Method

- **Features:** capacity retention and slope of the discharge-capacity curve over the first 20, 40, 60, 80 and 100 cycles (260 rows, 5 per cell).
- **Target:** log10 of the cell lifetime.
- **Models:** mean predictor, Ridge regression, gradient boosting.
- **Intervals:** split conformal prediction with a 90% target.
  - *Ordinary:* training and calibration rows are shuffled together, so the same cell can appear in both.
  - *Group-aware:* whole cells are assigned to training or calibration, never both.
- **Evaluation:** 50% training / 25% calibration / 25% test, split by cell and stratified by chemistry, repeated 20 times. An earlier 5-fold cross-validation run (also split by cell) was used as an exploratory check.

## Results (midterm)

Averages over 20 repeated 50/25/25 splits.

| Mean absolute error (cycles) | Random row split (leaky) | Split by cell |
|---|---|---|
| Mean predictor | 607 | 555 |
| Ridge | 148 | 158 |
| Gradient boosting | 62 | 143 |

| Method (target coverage 0.90) | Coverage | Coverage std | Width (log10) |
|---|---|---|---|
| Ordinary | 0.678 | 0.166 | 0.224 |
| Group-aware | 0.875 | 0.107 | 0.505 |

Ordinary calibration under-covers. Group-aware calibration comes closer to the target but gives wider intervals.

In the exploratory 5-fold run, group-aware coverage was higher in every chemistry and temperature group but reached only 0.688 at