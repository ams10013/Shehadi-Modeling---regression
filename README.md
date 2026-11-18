# Used car prices: should the dealer buy this car?

Linear regression that predicts a used car's lot sale price from its features, then uses that prediction to decide which cars to buy at wholesale auction.

## Files

| File | What it is |
|---|---|
| `analysis.ipynb` | All code and output. Run top to bottom to reproduce every number and chart. |
| `data/used_car_sales.csv` | 240 cars the dealer sold in the last year (used to fit and test the model) |
| `data/auction_lots.csv` | 40 cars from one recent auction (used only to test the buy/pass rule) |
| `figures/predicted_vs_actual.png` | Predicted vs actual sale price on the test cars, baseline vs v1 |
| `figures/auction_profit.png` | Total profit on the 40 auction lots, baseline rule vs v1 rule |

## How to run

```bash
pip install -r requirements.txt
jupyter nbconvert --to notebook --execute --inplace analysis.ipynb
```

The 70/30 split uses `random_state=42`, so results are identical on every run.

## Method

1. Randomly split the 240 sales into 168 training cars (70%) and 72 test cars (30%).
2. Fit OLS of `Sale_Price` on `Mileage_K`, `Age_Years`, `Horsepower`, `Accident`, `Num_Owners`. `Num_Owners` was not significant (p = 0.87) and is highly correlated with age and mileage, so it was dropped. The 4-feature model (v1) has a slightly higher adjusted R² (0.870 vs 0.869).
3. Compare v1 to a baseline that predicts the training-set average ($25,851) for every car, on the 72 test cars.
4. Decision rule: buy if auction price + $1,500 prep cost < predicted sale price. Applied to the 40 auction lots; profit per bought car = later sale price − auction price − $1,500.

## Results

**v1 coefficients (training fit)**, all p < 0.001:

| Feature | Effect on sale price |
|---|---|
| Mileage | −$782 per 10,000 miles |
| Age | −$1,292 per year |
| Horsepower | +$750 per 10 hp |
| Accident on history | −$2,177 |

**Fit on the 72 test cars**

| | R² | RMSE |
|---|---|---|
| Baseline (average price) | −0.01 | $6,494 |
| v1 regression | 0.82 | $2,716 |

**Decision on the 40 auction lots**

| Rule | Cars bought | Money-losing cars bought | Total profit |
|---|---|---|---|
| Baseline | 14 | 10 | −$6,000 |
| v1 | 17 | 6 | $15,800 |
