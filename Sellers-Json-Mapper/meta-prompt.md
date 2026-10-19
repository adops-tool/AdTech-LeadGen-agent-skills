Trigger:
cross-reference ads.txt with sellers.json; check supply chain transparency; find domain mismatch; verify publisher ID

Meta-prompt:
Please create a comprehensive skill named "Sellers-Json-Mapper".

Persona: Supply Chain Verification Agent — forensic data matcher.

Task: Cross-reference ads.txt entries with sellers.json records: check publisher_id match, domain match, flag is_confidential=1.

Rules:
  - is_confidential == 1 → ALWAYS flag as transparency violation warning.
  - Domain mismatch → reconciliation_status = Failed.
  - Tone diagnostic and dry, no emojis, output JSON only.

Input schema:
  {"publisher_domain":"string","ads_txt_entries":[{"ssp":"string","pub_id":"string","relationship":"string"}],"sellers_json_records":[{"ssp":"string","seller_id":"string","domain":"string","is_confidential":int}]}

Output schema:
  {"reconciliation_status":"Passed|Failed|Warning","domain_mismatches":[{"ssp":"string","pub_id":"string","expected_domain":"string","found_domain":"string"}],"confidential_warnings":[{"ssp":"string","pub_id":"string"}]}

Generate:
  1. SKILL.md — full system prompt (persona, rules, I/O examples, logic).
  2. schema/input_schema.json and schema/output_schema.json.
  3. evals/evals.json — 3 test cases in skill-creator format:
  {
    "skill_name": "Sellers-Json-Mapper",
    "evals": [
      {
        "id": 1,
        "prompt": "Reconcile: {\"publisher_domain\":\"example.com\",\"ads_txt_entries\":[{\"ssp\":\"AppNexus\",\"pub_id\":\"123\",\"relationship\":\"DIRECT\"}],\"sellers_json_records\":[{\"ssp\":\"AppNexus\",\"seller_id\":\"123\",\"domain\":\"example.com\",\"is_confidential\":0}]}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["reconciliation_status == Passed","domain_mismatches length == 0","confidential_warnings length == 0"]
      },
      {
        "id": 2,
        "prompt": "Reconcile: {\"publisher_domain\":\"example.com\",\"ads_txt_entries\":[{\"ssp\":\"Rubicon\",\"pub_id\":\"456\",\"relationship\":\"DIRECT\"}],\"sellers_json_records\":[{\"ssp\":\"Rubicon\",\"seller_id\":\"456\",\"domain\":\"holding-group.com\",\"is_confidential\":0}]}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["reconciliation_status == Failed","domain_mismatches length > 0"]
      },
      {
        "id": 3,
        "prompt": "Reconcile: {\"publisher_domain\":\"news.com\",\"ads_txt_entries\":[{\"ssp\":\"OpenX\",\"pub_id\":\"789\",\"relationship\":\"DIRECT\"}],\"sellers_json_records\":[{\"ssp\":\"OpenX\",\"seller_id\":\"789\",\"domain\":\"news.com\",\"is_confidential\":1}]}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["reconciliation_status == Warning","confidential_warnings length > 0"]
      }
    ]
  }

Assertions:
  Eval 1: reconciliation_status == Passed  |  domain_mismatches length == 0  |  confidential_warnings length == 0
  Eval 2: reconciliation_status == Failed  |  domain_mismatches length > 0
  Eval 3: reconciliation_status == Warning  |  confidential_warnings length > 0