# Screenshots — captions for PPT

1. **01-homepage.png** — Retail Agentic AI frontend, live on the S3 website
   endpoint (`retail-agentic-ai-frontend-227811178680-ap-south-1-an.s3-website.ap-south-1.amazonaws.com`).
   Confirms the static frontend deployment.
2. **02-model-wizard.png** — the Wizard flow, step 3 (Models), showing the
   backend's `/api/compatible-models` response correctly detecting which ML
   modules a given dataset supports.
3. **03-results-dashboard.png** — live results from the Customer Health & Churn
   model (RFM segmentation + churn risk) run against an uploaded dataset —
   confirms the full frontend → EC2 backend → ML model → response round trip
   works end-to-end.
