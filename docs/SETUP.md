# Setup and execution

## 1. Prepare Databricks

Use a Databricks workspace with serverless notebooks, Unity Catalog managed tables, Volumes, Lakeflow Jobs, and AI/BI dashboards available to your account. Free Edition quotas apply. These notebooks require the Databricks runtime environment, including `spark` and `display`; they are not standalone local Python scripts.

Create schema `ecommerce` in catalog `workspace`, then managed Volume `raw_files`. Use a dedicated project schema because writes replace the project tables.

## 2. Obtain source data

Download and extract the [original Olist dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce). Upload the five files listed in the README into `/Volumes/workspace/ecommerce/raw_files/`. Keep filenames unchanged. Uploading all nine original files is also fine; four are not consumed.

## 3. Import notebooks

Import all 13 `.ipynb` files from `notebooks/` into one workspace folder, preserving their base names. If the UI appends `.ipynb` to asset names, select actual assets when configuring job tasks or update exported paths accordingly. Use Python/serverless. Do not add `%run` chaining: the job tasks each read their dependencies from saved tables.

Run manually in numeric order for troubleshooting, or configure the job below. Always run the full notebook to reconstruct variables after a session restart. Old output cells were removed from this repository intentionally.

## 4. Configure Lakeflow Jobs

`job/ecommerce_lakehouse_pipeline.json` contains the exported 13-task sequential job with personal paths replaced by `/Workspace/Users/YOUR_EMAIL/...`.

Replace every `YOUR_EMAIL` path prefix with the actual workspace folder containing the imported notebooks. Preserve sequential dependencies and `max_concurrent_runs: 1`. Configure serverless notebook compute in the Jobs UI as needed by your workspace; exported JSON omits explicit cluster/environment assignments, so verify compatibility instead of assuming the file is universally deployable unchanged.

You can recreate the job through Jobs & Pipelines and select each corresponding notebook. Alternatively, after authenticating the Databricks CLI and adjusting JSON, use:

```bash
databricks jobs create --json @job/ecommerce_lakehouse_pipeline.json
```

Run on demand first. Do not add a recurring schedule merely to repeatedly refresh the same historical dataset. Wait for all tasks, including `13_pipeline_validation`, to succeed. Only one refresh should run at a time. Avoid manually writing tables during a job run.

## 5. Import dashboard

Import `dashboard/ecommerce_overview.lvdash.json` from the dashboard file import action. Verify the data references resolve to your Gold tables and choose available serverless SQL compute if requested. Review KPI values against README results, clear active filters, and verify percentage formatting: 6.77 is already percentage points, not 0.0677. Publish only with your intended viewer permissions. Import does not itself restore viewer permissions or make a dashboard public.

## 6. Verify

Expected final output: `All checks in this validation suite passed.` Verify 96,478 delivered orders; BRL 13,221,498.11 merchandise sales; 93,358 delivered-order customers; 2,801 repeat customers; and 6.77% lateness.

The snapshot guards intentionally stop on different row counts. Review changed input before adjusting expectations. A successful local syntax check is not a substitute for a Databricks run.

## 7. Publish to GitHub

Create a repository such as `olist-ecommerce-lakehouse`. Upload the **contents** of this project folder so README.md is at the repository root. Keep the source ZIP, raw CSVs, credentials, and private workspace exports out of the repository. Check notebook outputs remain cleared and job paths still contain a placeholder. The included screenshots were cropped, but inspect all images before uploading.

A successful-run screenshot was not included in the provided ZIP. Optionally add one showing all 13 tasks succeeded, with account details hidden, as `screenshots/pipeline_success.png`. The job is included as code regardless.
