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

Uses prompt engineering and structured output to classify messages into 16 request types, mark unsupported requests, or ask for clarification. Classified messages receive a historical default department and queue. No routing-review threshold is applied.

### Before running

- Install `pandas`, `httpx`, and `python-dotenv`.
- Ensure `data/processed/routing_catalog_selected.csv` is available.
- Create a local `.env` in the repository root using the course-aligned names:

```dotenv
# Replace the placeholder with your own API key.
OPENAI_API_KEY=your_actual_api_key_here

# Model used by the GenAI intake notebook.
OPENAI_MODEL=gpt-4.1-mini

# Model used by notebook 04.
DEFAULT_CHAT_MODEL=gpt-5-mini
```

Keep `.env` out of Git. Existing environment variables take precedence. Without a configured key, the notebook prompts privately; without a model setting, it uses `gpt-5-mini`.

Restart the kernel and run all cells from the repository root or `notebooks/`. A full run makes 46 API calls and may incur charges.

### Evaluation outputs

- `data/processed/genai_intake_evaluation.csv`: original 32 synthetic cases, with status and error tracking.
- `data/processed/genai_intake_boundary_evaluation.csv`: eight additional boundary cases, scored separately.

The notebook reports completion rate, accuracy among completed cases, and correctness over all cases. Expected API failures are recorded per case; unexpected programming errors remain visible. Routing accuracy is not evaluated.
