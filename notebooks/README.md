# Project Notebooks

## 01_data_preparation.ipynb

Prepares Boston’s 2023–2024 service request data for forecasting
and the GenAI intake assistant.

### Steps covered

- Load the two source CSV files.
- Filter to the four departments in scope.
- Check duplicate IDs, missing values, and timestamps.
- Create monthly demand summaries.
- Explore routing ambiguity.
- Prepare routing catalogs for the selected request types. 

### Before running

1. Activate the `boston311` Python environment.
2. Place both source CSV files in `data/raw/`.
3. Select the Python (boston311) notebook kernel.
4. Run all cells in order.

### Outputs

Processed CSV files are saved to `data/processed/`.

See the repository’s main README for findings, assumptions,
team responsibilities, and next steps.


## 02_demand_forecasting_agent.ipynb

Estimates monthly 2025 service-request demand for the four departments and provides a natural-language forecasting assistant.

### Steps covered

- Load and validate monthly demand summaries.
- Explore historical demand patterns.
- Create seasonal naïve forecasts using corresponding 2024 monthly counts.
- Evaluate historical performance by forecasting 2024 from 2023.
- Calculate MAE, WAPE, and mean error.
- Use a course-style LangChain ReAct agent to call forecasting and validation tools.
- Demonstrate agent responses and boundary checks.

### Before running

1. Activate the `boston311` Python environment.
2. Run `01_data_preparation.ipynb` to create the required input files.
3. Configure the API key and model settings in the repository-root `.env` file.
4. Select the Python (boston311) notebook kernel.
5. Run all cells in order.

### Outputs

- `data/processed/forecast_2025.csv`
- `outputs/forecast_validation_metrics.csv`
- `outputs/forecast_validation_2024.csv`
- Historical charts, validation tables, and agent explanations displayed in the notebook.

## 03_crew_planning.ipynb

Builds month-by-month staffing schedules for 2025 and supports natural-language questions about staffing policies and demand scenarios.

### Steps covered

- Load and validate the 2025 demand forecasts.
- Define crew capacities, costs, and staffing assumptions.
- Build fixed, hire-and-hold, and ISD release policies.
- Model hiring delays, releases, and Parks seasonal crews.
- Calculate monthly capacity, completed requests, backlog, and staffing costs.
- Use JSON extraction, Python validation, and LangGraph conditional routing.
- Demonstrate policy comparisons, monthly plans, and clarification behavior.
- Evaluate fixed staffing schedules under baseline and ±20% demand.

### Before running

1. Activate the `boston311` Python environment.
2. Run `02_demand_forecasting_agent.ipynb` to create the forecast file.
3. Configure the API key and model settings in the repository-root `.env` file.
4. Select the Python (boston311) notebook kernel.
5. Run all cells in order.

### Outputs

- `outputs/crew_plan_fixed.csv`
- `outputs/crew_plan_hire_and_hold.csv`
- `outputs/crew_plan_isd_release.csv`
- `outputs/crew_policy_summary.csv`
- `outputs/crew_sensitivity_monthly.csv`
- `outputs/crew_sensitivity_summary.csv`
- Calculated comparison tables, monthly schedules, and generated explanations.

### Limitations

The schedules are heuristic alternatives, not proven optimal solutions. Staffing costs exclude overdue penalties. Backlog represents unfinished workload and does not establish SLA violations.

## Part 4: GenAI intake (in 05_integrated_notebook.ipynb)

Classifies resident messages into 16 selected request types and provides historical department and queue suggestions.

### Steps covered

- Load the selected historical routing catalog.
- Define supported request types.
- Classify messages using prompt engineering and strict structured output.
- Validate returned fields and decision consistency.
- Handle unsupported requests and requests needing clarification.
- Look up historical routes for classified messages.
- Evaluate 32 baseline messages and eight additional boundary cases.
- Record classification results and API or response failures.

### Before running

Follow the setup instructions in the repository-root README, then run `05_integrated_notebook.ipynb` from a fresh kernel. Part 1 prepares the routing catalog used by Part 4.

### Outputs

- `data/processed/genai_intake_evaluation.csv`
- `data/processed/genai_intake_boundary_evaluation.csv`
- Classification decisions, clarification questions, historical route suggestions, and evaluation tables.

### Limitations

The evaluation uses a small synthetic test set. Classification accuracy does not establish department or queue accuracy. Historical route shares describe past assignments and are not model confidence scores.

## 05_integrated_notebook.ipynb

Combines data preparation, demand forecasting, crew planning, and resident intake into one end-to-end project notebook.

### Steps covered

- Configure shared project paths and environment settings.
- Prepare the historical data and routing catalogs.
- Generate and evaluate monthly 2025 demand forecasts.
- Build and compare staffing schedules.
- Evaluate sensitivity to changes in demand.
- Demonstrate the forecasting agent, crew-planning workflow, and intake assistant.
- Present findings, assumptions, and limitations.

### Before running

1. Activate the `boston311` Python environment.
2. Place both source CSV files in `data/raw/`.
3. Install the packages listed in the shared setup section.
4. Configure the API key and model settings in the repository-root `.env` file.
5. Select the Python (boston311) notebook kernel.
6. Restart the kernel and run all cells in order.

The integrated notebook runs the components sequentially; the individual notebooks do not need to be executed beforehand.

### Outputs

Processed datasets and evaluation files are saved to `data/processed/`. Forecast validation results, crew schedules, policy summaries, and sensitivity results are saved to `outputs/`.

Running the notebook overwrites generated files with the same names. Live API calls require an internet connection and may incur charges.

---

See the repository’s main README for findings, assumptions, team responsibilities, and next steps.