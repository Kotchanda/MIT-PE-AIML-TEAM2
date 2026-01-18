# Irish Health Insurance Chooser — lindy.ai Starter Package

## What this is
A starter pack of **structured files** you can import/host for a lindy.ai agent that:
- Asks a prioritized question flow
- Filters and scores plans deterministically
- Produces explainable plan recommendations
- Logs anonymous analytics for tuning

## Quick Start (no backend)
1. Upload these files to any static host (S3, GitHub Pages, etc.):
   - question_bank.json
   - plans.json (or plans.csv)
   - scoring_spec.json
2. In lindy.ai, create an agent and add tool calls:
   - GET question_bank.json
   - GET plans.json
3. Implement the logic described in `lindy_workflow_blueprint.md` using lindy steps/blocks.

## Populate real plan data
- Replace placeholder rows in `plans.csv` with real plans from **public plan pages and PDFs**.
- For each plan, set:
  - plan_id, insurer, plan_name
  - hospital_cover, plan_tier, key booleans
  - source_url (direct public link)
  - last_verified

## Compliance notes
- Do not scrape premiums from quote engines.
- Provide “information only” disclaimer.
- Always show sources (source_url).

## Suggested next step
Once you have 30–80 plans populated:
- Compute expected_info_gain dynamically as you filter.
- Start logging anonymous analytics to tune question order.