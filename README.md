# Cell-Disjoint Uncertainty Calibration for Early Battery-Life Prediction

Course project for 3000004032 – Advanced AI (Project 20).
Author: Hamza Irfan (262168013)

## Question

Does group-aware conformal calibration, which treats each battery cell as one unit instead of each cycle as an independent sample, give better-calibrated prediction intervals for early-cycle battery-life prediction?

## Data

SNL subset of the BatteryLife dataset (Tan et al., KDD 2025): 52 commercial Li-ion 18650 cells (LFP, NCA, NMC), 3 temperatures, 23 protocols.

The dataset is **not included** in this repository because of its size. To run the notebook:

1. Open https://zenodo.org/records/14969822 (use version v4).
2. Download `SNL.zip` and `Life labels.zip`.
3. Unzip both into a folder named `dataset` next to the notebook, so you have `dataset/SNL/` and `dataset/Life labels/`.

Please cite the original data paper: *Degradation of Commercial Lithium-Ion Cells as a Function of Chemistry and Cycling Conditions*, J. Electrochem. Soc., 2020, as well as BatteryLife.

## Method

- Features: capacity retention and slope of the discharge-capacity curve over the first 20, 40, 60, 80 and 100 cycles (260 rows, 5 per cell).
- Target: log10 of the cell lifetime.
- Models: mean predictor, Ridge, gradient boosting.
- Intervals: split conformal prediction at a 90% target, with ordinary calibration (random rows) and group-aware calibration (whole cells held out).
- All evaluation splits are by cell, repeated 20 times.

## Results so far (midterm)

| Split | Mean predictor | Ridge | Gradient boosting |
|---|---|---|---|
| Random row split (leaky), MAE in cycles | 611 | 147 | 43 |
| Split by cell, MAE in cycles | 610 | 159 | 120 |

| Method | Coverage (target 0.90) | Width (log10) |
|---|---|---|
| Ordinary | 0.679 | 0.192 |
| Group-aware | 0.889 | 0.508 |

Group-aware coverage is higher in every chemistry and temperature group, but only 0.688 at 15 °C. Results are based on 52 cells, so subgroup numbers are noisy.

## How to run

```
python -m venv venv
venv\Scripts\activate
pip install numpy pandas scikit-learn matplotlib ipykernel
```

Then open `battery_project.ipynb` in VS Code or Jupyter and run all cells.

## References

- Tan, R. et al. (2025). BatteryLife: A Comprehensive Dataset and Benchmark for Battery Life Prediction. KDD '25. https://doi.org/10.1145/3711896.3737372
- Angelopoulos, A. N. and Bates, S. (2021). A Gentle Introduction to Conformal Prediction and Distribution-Free Uncertainty Quantification. https://arxiv.org/abs/2107.07511
- Wu, K. et al. (2026). Uncertainty-Aware Early Battery Life Prediction with Composite-Kernel Gaussian Process Regression and Conformalized Adaptive Intervals. Algorithms, 19(7), 535. https://doi.org/10.3390/a19070535

## Status

Midterm stage. Planned next: per-chemistry calibration ablation, equal-width comparison, Gaussian process baseline, paper.