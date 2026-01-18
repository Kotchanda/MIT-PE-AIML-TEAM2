# lindy.ai Workflow Blueprint — Irish Health Insurance Chooser (v1)

This package is designed to run as a **lindy.ai agent** that:
- Loads a **Question Bank** and **Plans Dataset**
- Asks questions in a **priority-driven** order
- Applies **hard filters** + **weighted scoring**
- Returns **explainable recommendations** with sources
- Logs **anonymous analytics** for iterative tuning

## Files in this package
- `question_bank.json` — question definitions + elimination rules + priority weights
- `plans.csv` / `plans.json` — normalized plan dataset (starter placeholders)
- `benefit_dictionary.json` — canonical field definitions
- `scoring_spec.json` — hard filters + weights + explainability settings
- `session_analytics_schema.json` — what to log anonymously
- `lindy_tool_contracts.md` — recommended API endpoints and payloads

## lindy.ai Blocks (recommended)
1. **Start + Disclaimer**
   - Message: “Information only; verify benefits/pricing with the provider.”
2. **Load Question Bank** (Tool call)
   - GET `question_bank.json`
3. **Load Plans Dataset** (Tool call)
   - GET `plans.json` (or `plans.csv`)
4. **Initialize Session State**
   - `answers = {}`
   - `remaining_plans = all_plans`
   - `asked_questions = []`
5. **Question Loop**
   - Compute `effective_priority` for unanswered questions:
     - base_priority
     - + expected_info_gain (how much it splits remaining_plans)
     - + popularity_score (from analytics; default 0.5)
     - - friction_score (from analytics; default 0.2)
   - Ask next question (chat)
   - Save answer
   - Update “Questionnaire Table” panel text (see UI section below)
6. **Apply Hard Filters**
   - Apply elimination rules from question bank
   - Apply additional hard filters from `scoring_spec.json`
7. **Score Plans**
   - Compute normalized features → weighted score
   - Apply tier preference multiplier from Q_PRICE_POSTURE
8. **Generate Explainable Output**
   - For top 3 plans:
     - matched must-haves
     - top 3 drivers (largest weight contributions)
     - trade-offs (missing features vs runner-up)
     - sources (plan `source_url`)
9. **Render Results**
   - Cards: plan name, insurer, fit score, reasons, trade-offs, source link
   - Provide “Compare” view (side-by-side bullets)
10. **Feedback + Anonymous Logging**
   - Ask: “Was this helpful?” (thumbs up/down)
   - Log session analytics (no personal data)

## UI pattern (works well for web embeds)
- **Chat area**: question-by-question
- **Right panel (or expandable section)**: “Your Answers” summary table
- **Result cards**: top 3 + compare

## Data governance
- Version the dataset (e.g., `plans_YYYY-MM-DD.json`)
- Keep `source_url` and `last_verified` current
- Avoid scraping quote engines (premiums); allow users to provide budget as preference if needed.