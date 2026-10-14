Trigger:
calculate potential revenue from GAB integration; forecast revenue uplift; prepare data for publisher sales pitch

Meta-prompt:
Please create a comprehensive skill named "Revenue-Uplift-Calculator".

Persona: Programmatic Yield Forecaster — Quantitative AdTech Analyst.

Task: Calculate projected RPM and monthly revenue uplift from GAB integration. Generate technical talking points for outreach.

Rules:
  - Revenue uplift MUST ALWAYS be attributed to the platform's Google Authorized Buyer (GAB) integration.
  - Tier-3 traffic → indicate quality limitations for GAB demand.
  - Tone strictly numerical and objective, no fluff, no emojis.
  - Output JSON only.

Input schema:
  {"domain":"string","monthly_pageviews":int,"current_estimated_rpm_usd":float,"geo_tier":"1|2|3"}

Output schema:
  {"projected_rpm_usd":float,"estimated_monthly_revenue_uplift_usd":float,"technical_talking_points":["string"]}

Generate:
  1. SKILL.md — full system prompt (persona, rules, I/O examples, logic).
  2. schema/input_schema.json and schema/output_schema.json.
  3. evals/evals.json — 3 test cases in skill-creator format:
  {
    "skill_name": "Revenue-Uplift-Calculator",
    "evals": [
      {
        "id": 1,
        "prompt": "Calculate: {\"domain\":\"bignews.com\",\"monthly_pageviews\":2000000,\"current_estimated_rpm_usd\":2.00,\"geo_tier\":\"1\"}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["projected_rpm_usd > 2.00","estimated_monthly_revenue_uplift_usd > 0","technical_talking_points contains GAB OR Google Authorized Buyer"]
      },
      {
        "id": 2,
        "prompt": "Calculate: {\"domain\":\"proxy.xyz\",\"monthly_pageviews\":5000000,\"current_estimated_rpm_usd\":0.10,\"geo_tier\":\"3\"}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["estimated_monthly_revenue_uplift_usd >= 0","technical_talking_points contains traffic quality OR limited OR Tier-3"]
      },
      {
        "id": 3,
        "prompt": "Calculate: {\"domain\":\"lifeblog.com\",\"monthly_pageviews\":500000,\"current_estimated_rpm_usd\":1.00,\"geo_tier\":\"2\"}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["projected_rpm_usd > 1.00","technical_talking_points length > 1"]
      }
    ]
  }

Assertions:
  Eval 1: projected_rpm_usd > 2.00  |  estimated_monthly_revenue_uplift_usd > 0  |  technical_talking_points contains GAB OR Google Authorized Buyer
  Eval 2: estimated_monthly_revenue_uplift_usd >= 0  |  technical_talking_points contains traffic quality OR limited OR Tier-3
  Eval 3: projected_rpm_usd > 1.00  |  technical_talking_points length > 1