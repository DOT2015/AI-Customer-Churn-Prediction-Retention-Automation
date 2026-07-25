# AI-Customer-Churn-Prediction-Retention-Automation

 
An n8n workflow that runs on a daily schedule, pulls active customer data
from PostgreSQL, engineers behavioral features, uses an LLM agent to score
churn risk, and routes the result to Gmail + Slack alerts or a database
insert depending on risk level.
 
## Architecture
 
Schedule Trigger (daily, 2am)
→ 5 parallel PostgreSQL queries (customer profile, login activity,
  transactions, support tickets, product usage)
→ Merge (joined on customer_id)
→ Feature Engineering (Code node — computes engagement score, risk score,
  risk level, churn indicators)
→ AI Agent (gpt-5-mini, structured output parser)
→ If risk_score > 20:
    → Gmail alert + Slack alert ("customer-churn" channel)
  else:
    → Insert prediction into churn_predictions table
 
## Technologies
 
- n8n
- PostgreSQL
- OpenAI gpt-5-mini
- Gmail API
- Slack API
## Known issue
 
The current branch logic only writes to `churn_predictions` on the
**low-risk** path — high-risk predictions that trigger alerts are not
currently persisted to the database. Fix before treating this as a
complete audit trail.
 
## Sample Output (from Structured Output Parser schema)
 
```json
{
  "risk_score": 45,
  "risk_level": "MEDIUM",
  "top_reasons": [
    "Low product usage",
    "Negative support experience",
    "Frequent support tickets"
  ],
  "recommended_action": "Assign Customer Success Manager and schedule a retention call."
}
```
 
## Installation
 
1. Clone repository
2. Import `workflow/customer-churn-workflow.json` into n8n
3. Configure credentials: PostgreSQL, OpenAI, Gmail, Slack
4. Run `database/schema.sql` against your Postgres instance
5. Activate the workflow
## License
 
MIT
