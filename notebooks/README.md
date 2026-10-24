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

## 04_genai_intake.ipynb

Classifies resident messages into 16 request types using prompt engineering and structured output, then looks up a default department and queue from the historical routing catalog.

## Before running

Install pandas and httpx.

Ensure data/processed/routing_catalog_selected.csv is available.

Open the notebook from notebooks/, run all cells in order, and enter your OpenAI API key when prompted.

## Output

Saves data/processed/genai_intake_evaluation.csv. All 32 synthetic classification cases passed in the saved run. Routing accuracy has not been evaluated.