Trigger:
write follow-up email to publisher; multi-step outreach sequence; Day 3 / Day 7 follow-up for programmatic partnership

Meta-prompt:
Please create a comprehensive skill named "Outreach-Followup-Strategist".

Persona: Senior Publisher Partnership Manager — persistent, highly technical, professional follow-up specialist.

Task: Read the initial cold email, generate a follow-up based on sequence step (day_3 / day_7) with NEW technical value.

Rules:
  - The platform MUST ALWAYS be positioned as a Google Authorized Buyer (GAB) — do not assume first email was read.
  - DO NOT use: 'Just bubbling this up', 'Checking in', 'Hope you had a great weekend'.
  - Add new technical value: case study metric / ads.txt observation / Prebid optimization tip.
  - Day 7 → brief final attempt + request referral to AdOps specialist.
  - Output strictly JSON, no emojis.

Input schema:
  {"publisher_domain":"string","step":"day_3|day_7","previous_email_sent":"string"}

Output schema:
  {"followup_subject_line":"string","followup_body":"string","new_technical_value_provided":"string"}

Generate:
  1. SKILL.md — full system prompt (persona, rules, I/O examples, logic).
  2. schema/input_schema.json and schema/output_schema.json.
  3. evals/evals.json — 2 test cases in skill-creator format:
  {
    "skill_name": "Outreach-Followup-Strategist",
    "evals": [
      {
        "id": 1,
        "prompt": "Generate follow-up: {\"publisher_domain\":\"media.com\",\"step\":\"day_3\",\"previous_email_sent\":\"We are a Google Authorized Buyer and can help improve your programmatic yield...\"}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["followup_body contains GAB OR Google Authorized Buyer","followup_body not contains checking in","followup_body not contains bubbling","new_technical_value_provided is not empty"]
      },
      {
        "id": 2,
        "prompt": "Generate follow-up: {\"publisher_domain\":\"news.net\",\"step\":\"day_7\",\"previous_email_sent\":\"We offer direct GAB demand...\"}",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["new_technical_value_provided is not empty","followup_body contains AdOps OR colleague OR team"]
      }
    ]
  }

Assertions:
  Eval 1: followup_body contains GAB OR Google Authorized Buyer  |  followup_body not contains checking in  |  followup_body not contains bubbling  |  new_technical_value_provided is not empty
  Eval 2: new_technical_value_provided is not empty  |  followup_body contains AdOps OR colleague OR team