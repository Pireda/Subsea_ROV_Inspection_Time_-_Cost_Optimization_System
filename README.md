# Subsea ROV Inspection Time & Cost Optimization System (MVP)

Production-style portfolio project that simulates subsea ROV inspection missions and applies machine learning to predict:

- **Operational inspection time**
- **Total operational cost**

It combines simplified offshore engineering physics with an ML forecasting layer and a Streamlit operator dashboard.

## Project Structure

```text
subsea_time_cost_system/
├── app.py
├── data_simulation.py
├── train_models.py
├── missions.csv                  # generated after simulation/training
├── models/
│   ├── time_model.pkl
│   ├── cost_model.pkl
│   └── scaler.pkl
├── requirements.txt
└── README.md
```

## Engineering Logic (Ground Truth)

Operational behavior is simulated using:

- `current_penalty = 0.35 * current_speed`
- `depth_penalty = 0.00008 * depth`
- `effective_speed = rov_speed - (current_penalty + depth_penalty)`
- `effective_speed >= 0.3 m/s`
- `time_ideal = line_length / (effective_speed * 3600)`

Real operation includes inefficiency:

- `inefficiency = 0.05 * current_speed + 0.00005 * depth + noise`
- `time_real = time_ideal * (1 + inefficiency)`

Cost model:

- `operational_cost = time_real * (vessel_cost_per_hour + rov_cost_per_hour)`
- `energy_consumption = power * time_real`
- `energy_cost = energy_consumption * energy_cost_per_kwh`
- `total_cost = operational_cost + energy_cost`

## Setup

```bash
cd subsea_time_cost_system
python -m venv .venv
source .venv/bin/activate  # Linux/macOS
pip install -r requirements.txt
```

## Generate Data

```bash
python data_simulation.py
```

This creates `missions.csv` with 4000 synthetic missions.

## Train Models

```bash
python train_models.py
```

Outputs:

- `models/time_model.pkl`
- `models/cost_model.pkl`
- `models/scaler.pkl`

Training uses:

- RandomForestRegressor (time)
- RandomForestRegressor (cost)
- 80/20 train-test split
- StandardScaler
- Metrics: MAE, RMSE, R²

## Run Dashboard

```bash
streamlit run app.py
```

Sidebar controls let users configure mission conditions and operational economics.

Main outputs include:

- Predicted inspection time (hours + minutes)
- Predicted total cost (USD)
- Estimated energy consumption (kWh)
- Estimated energy cost (USD)

The dashboard also provides a Plotly sensitivity chart showing how **current speed** influences predicted total cost.

## Future Expansion

- Risk module integration (weather windows, failure probabilities)
- Route segmentation and variable current profiles
- Constraint-based optimization for vessel and ROV scheduling
- Scenario comparison and report export
