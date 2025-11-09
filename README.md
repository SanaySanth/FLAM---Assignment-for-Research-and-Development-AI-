# # FLAM R&D Technical Assessment – Parametric Curve Fitting 
This project estimates three parameters (θ, M, X) of a parametric curve using nonlinear least squares optimization. The model is fitted to observed (x, y) data with a robust Huber loss to minimize error. Results show an accurate curve fit with θ = 28.13°, M = 0.0214, and X = 54.89, confirming strong model performance.



## 1. Problem Overview

We are given a dataset containing measured coordinates (x, y).
The true curve is defined parametrically by:

[
\begin{cases}
x(t) = t \cos(\theta) - e^{M|t|}\sin(0.3t)\sin(\theta) + X \
y(t) = 42 + t \sin(\theta) + e^{M|t|}\sin(0.3t)\cos(\theta)
\end{cases}
]

where ( 6 \le t \le 60 ) and the unknowns satisfy:
[
0 < \theta < 50,\quad -0.05 < M < 0.05,\quad 0 < X < 100
]

The goal is to find values of θ, M, and X that make the model best match the observed (x, y) data, and then measure the L₁ distance between the fitted curve and the observations.

---

## 2. Files in this Repository

| File                         | Purpose                                                                                                                          |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| **xy_data.csv**              | Provided dataset containing measured x, y values.                                                                                |
| **R&D.py**  | Base script – fits θ, M, X using nonlinear least squares (Huber loss) and plots the fitted curve. Extended version with 9 detailed visualizations, sensitivity analysis, uncertainty estimation, and comparison of loss functions.                               |

                                                                    |

---

## 3. Approach and Methodology

1. **Data Handling**
   The CSV contained columns `x` and `y`.
   Since `t` wasn’t given, I assumed the points are uniformly sampled along the range **6 ≤ t ≤ 60**, which is consistent with the problem statement.

2. **Model Implementation**
   The equations above were implemented as a vectorized Python function `model_xy(t, theta, M, X)`, using NumPy for speed and clarity.

3. **Optimization**
   The parameters were estimated using `scipy.optimize.least_squares`.
   The solver minimizes the combined residuals of x and y:
   [
   r = [x_{\text{model}} - x_{\text{obs}},\ y_{\text{model}} - y_{\text{obs}}]
   ]
   under the specified parameter bounds.

4. **Robust Loss Function**
   I used the **Huber loss**, which behaves like L² for small residuals and L¹ for large residuals.
   This helps reduce the effect of outliers while keeping smooth convergence.

5. **Evaluation Metric**
   After fitting, the **L₁ distance** was computed as:
   [
   \text{L₁} = \sum_i (|x_{\text{model}} - x_{\text{obs}}| + |y_{\text{model}} - y_{\text{obs}}|)
   ]
   The fitted curve and observed points were plotted together for a visual check.

---

## 4. Constants Explained (Why These Values)

| Constant                               | Value                                                                                                                                | Reason                                                     |    |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- | -- |
| **T_MIN, T_MAX**                       | 6.0, 60.0                                                                                                                            | Defines the domain of t as given in the problem statement. |    |
| **N_UNIFORM = 1000**                   | Number of uniform t samples used to draw a smooth curve and compute metrics. 1000 gives a good balance between resolution and speed. |                                                            |    |
| **THETA_BOUNDS_DEG = (1e-6, 50-1e-6)** | θ in degrees, slightly offset from exact endpoints to avoid numerical issues at 0° or 50°.                                           |                                                            |    |
| **M_BOUNDS = (-0.05+1e-8, 0.05-1e-8)** | Boundaries prevent exponential blow-up or decay in e^{M                                                                              | t                                                          | }. |
| **X_BOUNDS = (1e-8, 100-1e-8)**        | Small epsilons keep X inside the open interval (0,100).                                                                              |                                                            |    |

These constants make sure the optimization stays stable and physically valid.

---

## 5. Results (Huber Fit)

| Parameter  | Symbol | Value        |
| ---------- | ------ | ------------ |
| Angle      | θ      | ≈ **28.13°** |
| Modulation | M      | ≈ **0.0214** |
| Offset     | X      | ≈ **54.89**  |

**L₁ error:** ~37,865
**L₂ norm:** ~881
The fit visually aligns well with the data and satisfies all constraints.

The fitted curve equations:

[
\begin{cases}
x(t) = t\cos(28.135°) - e^{0.0214|t|}\sin(0.3t)\sin(28.135°) + 54.890 \
y(t) = 42 + t\sin(28.135°) + e^{0.0214|t|}\sin(0.3t)\cos(28.135°)
\end{cases}
]

---

## 6. Bonus / R&D Extensions

To make the analysis more complete and research-oriented, I expanded the project with additional diagnostics and visualizations.

### Extra Steps in `bonus_visuals_display.py`

* **Compared three loss functions:** Huber, Soft-L1, and Linear (L²).
* **Computed parameter sensitivity:** Cost vs θ, M, X plots to verify the fit is stable and identifiable.
* **Residual diagnostics:**
  Residuals vs t, histogram, x-residual vs y-residual, residuals vs predicted magnitude.
* **Parameter uncertainty:**
  Approximate covariance matrix from the Jacobian (`Cov ≈ σ² (JᵀJ)⁻¹`), giving rough standard errors.
* **Comprehensive visualizations:**
  Nine figures display all diagnostic plots interactively (using `plt.show()`).

### Why it Matters

These analyses show that:

* The fit isn’t just good visually; it’s statistically consistent.
* The model behaves robustly to outliers.
* Parameters are well constrained (clear cost minima).
* The method is reproducible and transparent — traits expected in R&D work.

---

## 7. How to Run

### Basic Version

```bash
python R&D.py
```

Displays fitted curve and prints θ, M, X values.


Shows all 9 visualizations interactively.
Set `SAVE_RESULTS = True` inside the script to also export PNGs and summaries.

Dependencies:

```bash
pip install numpy pandas matplotlib scipy
```

---

## 8. Key Learnings and Takeaways

* Learned how to translate a mathematical parametric equation into vectorized code.
* Understood the practical difference between L₁, L₂, and Huber losses.
* Gained experience using SciPy’s nonlinear optimizer with bounds and robust losses.
* Explored residual analysis and parameter sensitivity as quality checks.
* Practiced writing clear, explainable research-style documentation.

---

**Author:** *SANAY SANTH V*
**Date:** * 09/11/2025*
**For:** FLAM R&D Technical Assessment

