Trigger:
audit Prebid.js config; check bidderTimeout; evaluate header bidding setup; find latency issues in Prebid

Meta-prompt:
Please create a comprehensive skill named "Prebid-Config-Auditor".

Persona: Header Bidding Architecture Expert — Senior Yield Engineer.

Task: Analyze pbjs.setConfig() and adUnits. Flag timeout issues, bidder density, price granularity problems.

Rules:
  - bidderTimeout > 2000ms → warning (too slow); < 500ms → warning (high timeout risk).
  - More than 8 bidders per ad unit → latency risk warning.
  - priceGranularity == 'low' for premium inventory → flag.
  - Output strictly JSON, no markdown blocks.

Input schema:
  JSON payload of pbjs.setConfig() and adUnits definitions

Output schema:
  {"audit_status":"Pass|Needs_Optimization|Critical_Issues","timeout_analysis":"string","bidder_density_warnings":["string"],"granularity_check":"string","optimized_config_recommendation":{}}

Generate:
  1. SKILL.md — full system prompt (persona, rules, I/O examples, logic).
  2. schema/input_schema.json and schema/output_schema.json.
  3. evals/evals.json — 3 test cases in skill-creator format:
  {
    "skill_name": "Prebid-Config-Auditor",
    "evals": [
      {
        "id": 1,
        "prompt": "Audit: {\"bidderTimeout\":3500,\"priceGranularity\":\"custom\",\"adUnits\":[{\"code\":\"banner\",\"bids\":[{\"bidder\":\"a\"},{\"bidder\":\"b\"},{\"bidder\":\"c\"},{\"bidder\":\"d\"},{\"bidder\":\"e\"},{\"bidder\":\"f\"},{\"bidder\":\"g\"},{\"bidder\":\"h\"},{\"bidder\":\"i\"},{\"bidder\":\"j\"},{\"bidder\":\"k\"},{\"bidder\":\"l\"}]}]}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["audit_status == Critical_Issues","timeout_analysis contains 3500 OR too slow OR high","bidder_density_warnings length > 0"]
      },
      {
        "id": 2,
        "prompt": "Audit: {\"bidderTimeout\":400,\"priceGranularity\":\"dense\",\"adUnits\":[{\"code\":\"banner\",\"bids\":[{\"bidder\":\"a\"},{\"bidder\":\"b\"}]}]}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["audit_status == Needs_Optimization","timeout_analysis contains 400 OR too aggressive OR low"]
      },
      {
        "id": 3,
        "prompt": "Audit: {\"bidderTimeout\":1200,\"priceGranularity\":\"dense\",\"adUnits\":[{\"code\":\"banner\",\"bids\":[{\"bidder\":\"a\"},{\"bidder\":\"b\"},{\"bidder\":\"c\"},{\"bidder\":\"d\"},{\"bidder\":\"e\"}]}]}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["audit_status == Pass"]
      }
    ]
  }

Assertions:
  Eval 1: audit_status == Critical_Issues  |  timeout_analysis contains 3500 OR too slow OR high  |  bidder_density_warnings length > 0
  Eval 2: audit_status == Needs_Optimization  |  timeout_analysis contains 400 OR too aggressive OR low
  Eval 3: audit_status == Pass