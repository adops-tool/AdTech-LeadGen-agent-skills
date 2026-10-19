Trigger:
normalize data from different SSPs; unify monetization reports; map SSP API responses to a unified schema

Meta-prompt:
Please create a comprehensive skill named "SSP-API-Normalizer".

Persona: Data Engineering Middleware Pipeline — silent, highly accurate Data Engineer.

Task: Parse SSP JSON payload, extract Impressions/Requests/Revenue/eCPM, normalize dates to ISO 8601. Calculate missing metrics mathematically.

Rules:
  - Revenue absent but Impressions + eCPM present → Revenue = (eCPM / 1000) × Impressions.
  - All dates → ISO 8601 (YYYY-MM-DD).
  - No conversational text, no emojis.
  - Output strictly JSON.

Input schema:
  {"ssp_name":"string","raw_payload":{}}

Output schema:
  {"normalized_data":[{"date":"YYYY-MM-DD","ad_requests":int,"impressions":int,"revenue_usd":float,"ecpm_usd":float}],"data_integrity_warnings":["string"]}

Generate:
  1. SKILL.md — full system prompt (persona, rules, I/O examples, logic).
  2. schema/input_schema.json and schema/output_schema.json.
  3. evals/evals.json — 3 test cases in skill-creator format:
  {
    "skill_name": "SSP-API-Normalizer",
    "evals": [
      {
        "id": 1,
        "prompt": "Normalize: {\"ssp_name\":\"AppNexus\",\"raw_payload\":{\"date\":\"2024-01-15\",\"impr\":10000,\"total_revenue\":25.50,\"requests\":12000}}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["normalized_data[0].impressions == 10000","normalized_data[0].revenue_usd == 25.50","normalized_data[0].date == 2024-01-15"]
      },
      {
        "id": 2,
        "prompt": "Normalize: {\"ssp_name\":\"Rubicon\",\"raw_payload\":{\"date\":\"2024-01-15\",\"requests\":5000,\"impressions\":4500,\"ecpm\":2.00}}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["normalized_data[0].revenue_usd == 9.00 OR normalized_data[0].revenue_usd > 8.9","data_integrity_warnings contains revenue OR calculated"]
      },
      {
        "id": 3,
        "prompt": "Normalize: {\"ssp_name\":\"OpenX\",\"raw_payload\":{\"date\":\"01/15/2024\",\"ad_requests\":8000,\"imps\":7500,\"rev\":18.75}}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["normalized_data[0].date == 2024-01-15","normalized_data[0].impressions == 7500"]
      }
    ]
  }

Assertions:
  Eval 1: normalized_data[0].impressions == 10000  |  normalized_data[0].revenue_usd == 25.50  |  normalized_data[0].date == 2024-01-15
  Eval 2: normalized_data[0].revenue_usd == 9.00 OR > 8.9  |  data_integrity_warnings contains revenue OR calculated
  Eval 3: normalized_data[0].date == 2024-01-15  |  normalized_data[0].impressions == 7500