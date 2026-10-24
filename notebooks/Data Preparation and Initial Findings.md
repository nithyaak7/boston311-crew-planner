## **Data Preparation and Initial Findings**

### **Purpose**

Prepare Boston’s 2023–2024 service request data for two project components:

1. Forecasting monthly demand and planning crews for 2025\.  
2. Building a GenAI intake assistant that recommends request types, departments, and queues.

### **What We Did**

#### **1\. Set up the development environment**

* Created the shared GitHub repository.  
* Configured `.gitignore` to exclude raw datasets, secrets, and local environment files.  
* Created the `boston311` Conda environment using Python 3.11.  
* Connected the environment to the data-preparation notebook in VS Code.

#### **2\. Loaded and filtered the historical data**

We loaded both years and retained the four departments specified in the challenge.

| Department | Code | 2023 requests | 2024 requests |
| ----- | ----- | ----- | ----- |
| Public Works | PWDx | 171,268 | 157,610 |
| Transportation | BTDT | 75,314 | 81,447 |
| Parks & Recreation | PARK | 17,541 | 18,736 |
| Inspectional Services | ISD | 18,123 | 18,624 |
| **Total in scope** |  | **282,246** | **276,417** |

The resulting dataset contains **558,663 requests**.

#### **3\. Checked data quality**

| Check | Result |
| ----- | ----- |
| Exact duplicate rows in the loaded analysis columns | 0 |
| Missing request IDs | 0 |
| Rows with repeated request IDs | 0 |
| Missing opening dates | 0 |
| Missing request types | 0 |
| Missing queues | 0 |
| Missing SLA target dates | 82,143 |
| Missing closure dates | 51,814 |

We converted opening, target, and closure timestamps into datetime values.

All date validation checks passed:

* No parsing failures.  
* No opening dates outside their source year.  
* No closure dates before opening.  
* No target dates before opening.

Missing target and closure dates were preserved. Missing closure dates alone should not be interpreted as overdue status.

We also observed an example labeled `ONTIME` despite having no SLA target date. Historical SLA analysis will therefore distinguish recorded status labels from performance calculated using valid dates.

#### **4\. Created monthly demand summaries**

We counted requests by department and opening month.

The resulting `monthly_demand.csv` contains:

* **96 rows:** four departments across 24 months.  
* Columns: `department`, `month`, and `request_count`.  
* All expected department-month combinations.  
* Counts that reconcile to the 558,663 requests in scope.

All request types within the four departments remain included in demand analysis.

#### **5\. Built historical routing catalogs**

We grouped records by request type, department, and queue, retaining the number of requests associated with each combination.

Initial exploration showed that a request type can appear in many queues. For example, street-cleaning requests appeared across geographic districts and specialist queues.

Comparing neighborhoods with queues showed that neighborhood alone does not uniquely identify a route. Dorchester requests, for example, appeared in both North and South Dorchester queues.

These are historical associations. The data does not establish whether an alternative route reflects a transfer, a special case, or inconsistent classification.

#### **6\. Selected the assistant’s initial coverage**

We selected 16 request types for an initial, manageable intake scope.

| Primary department focus | Selected request types |
| ----- | ----- |
| Transportation | Parking Enforcement; Sign Repair; Abandoned Vehicles; Traffic Signal Inspection |
| Inspectional Services | Rodent Activity; Unsatisfactory Living Conditions; Pest Infestation \- Residential; Heat \- Excessive Insufficient |
| Parks & Recreation | Tree Maintenance Requests; Ground Maintenance; Equipment Repair; New Tree Requests |
| Public Works | Mattress\_Pickup; Schedule a Bulk Item Pickup; Missed Trash/Recycling/Yard Waste/Bulk Item; Request for Pothole Repair |

This table describes the selection focus, not exclusive department ownership. All observed routing alternatives for these types were retained.

Repeated whitespace was normalized in a separate `type_label` field, while original request-type labels were preserved.

### **What We Found**

#### **Demand differs across departments and years**

Public Works had the largest request volume in both years. Its volume declined from 2023 to 2024, while the other three departments increased.

Annual totals provide context, but monthly patterns must be examined before estimating 2025 demand.

#### **Routing complexity varies substantially**

| Request type | Leading historical queue | Share of selected type records |
| ----- | ----- | ----- |
| Mattress\_Pickup | PWDx\_WM\_Mattress\_Pickup | 100.0% |
| Parking Enforcement | BTDT\_Parking Enforcement | 99.4% |
| Rodent Activity | ISD\_Environmental Services (INTERNAL) | 94.1% |
| Tree Maintenance Requests | PARK\_Tree Maintenance Request | 82.5% |
| Pest Infestation \- Residential | ISD\_Environmental Services (INTERNAL) | 58.9% |
| Ground Maintenance | PARK\_Maintenance\_Region 1 | 20.5% |
| Equipment Repair | PARK\_Maintenance\_Trades | 18.9% |
| Request for Pothole Repair | PWDx\_Contractor Complaints | 13.4% |

Percentages are rounded to one decimal place. A displayed 100.0% does not necessarily mean there are no alternative routes.

Historical route share is **not model confidence or proof that a queue is correct for a new complaint**.

#### **A single-route filter was too restrictive**

Restricting coverage to types with only one observed route excluded many common resident problems and left poor Parks coverage.

We therefore retained common request types and their routing alternatives rather than limiting the assistant to single-route categories.

### **Proposed Intake Routing Policy**

For the initial prototype:

* Types with a leading historical route share of at least 90% may receive a historical route suggestion when the message clearly matches.  
* Types below 90% require context-based review before queue selection.  
* Conflicting, missing, or insufficient information should trigger clarification or manual review.  
* When a queue cannot be justified, return `queue=None` and `needs_review=True`.

The **90% threshold is a provisional project assumption**. It must be evaluated using labeled messages and may be revised.

Queue names containing `(INTERNAL)` are preserved as recorded labels; the label alone does not establish whether the underlying issue can be reported by a resident.

### **Prepared Outputs**

| File | Purpose |
| ----- | ----- |
| `data/processed/monthly_demand.csv` | Historical monthly counts for forecasting |
| `data/processed/routing_catalog_full.csv` | All observed request type–department–queue combinations |
| `data/processed/routing_catalog_selected.csv` | All observed routes for the 16 supported types |

The next handoff file is `data/processed/routing_review.csv`, containing leading routes, historical shares, and the proposed review policy.

### **Next Steps**

1. **Complete the data handoff**  
   * Save the routing-review policy.  
   * Verify the notebook runs from a fresh kernel.  
   * Commit the notebook and processed outputs to GitHub.  
2. **Develop the demand forecast**  
   * Examine monthly patterns by department.  
   * Implement and validate a simple forecasting baseline.  
   * Produce 48 forecasts for 2025: four departments across 12 months.  
3. **Build the staffing model**  
   * Apply the challenge’s crew capacities, costs, starting levels, hiring delays, release rules, and Parks seasonal options.  
   * Compare staffing, capacity, backlog, and costs under baseline and ±20% demand.  
   * Document how deadlines are modeled before estimating overdue penalties.  
4. **Build and evaluate the GenAI assistant**  
   * Add human-reviewed descriptions to the selected catalog.  
   * Implement the agreed course-stack workflow.  
   * Validate outputs against observed routing combinations.  
   * Evaluate on 30–50 labeled resident messages kept separate from prompt-development examples.

### **Current Status and Limitations**

Data validation, monthly aggregation, and initial routing analysis are complete.

Forecasting, staffing calculations, historical SLA analysis, and the GenAI assistant have not yet been completed. No model accuracy, staffing savings, or service improvements have been established.

Monthly capacity and backlog will be reported separately from SLA on-time performance.

