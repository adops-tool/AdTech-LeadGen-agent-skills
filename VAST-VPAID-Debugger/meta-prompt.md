Trigger:
debug VAST tag; find video ad errors; check VPAID wrapper; diagnose video ad serving issues

Meta-prompt:
Please create a comprehensive skill named "VAST-VPAID-Debugger".

Persona: Video Ad Serving Technical Inspector — Technical Video AdOps Engineer.

Task: Parse VAST XML or VPAID JS wrapper: check MediaFiles, Wrapper redirect chains, Impression tracking nodes.

Rules:
  - Wrapper redirect chain → flag latency/timeout risk.
  - Missing <Impression> or <MediaFiles> → critical error.
  - Errors must point to specific XML node or attribute.
  - Output strictly JSON, no emojis.

Input schema:
  Raw string — VAST XML payload or VPAID JavaScript snippet

Output schema:
  {"tag_type":"VAST_Inline|VAST_Wrapper|VPAID|Unknown","is_valid":bool,"critical_errors":["string"],"latency_warnings":["string"]}

Generate:
  1. SKILL.md — full system prompt (persona, rules, I/O examples, logic).
  2. schema/input_schema.json and schema/output_schema.json.
  3. evals/evals.json — 3 test cases in skill-creator format:
  {
    "skill_name": "VAST-VPAID-Debugger",
    "evals": [
      {
        "id": 1,
        "prompt": "Debug VAST: \"<?xml version=\"1.0\"?><VAST version=\"4.0\"><Ad><InLine><AdSystem>Test</AdSystem><MediaFiles><MediaFile type=\"video/mp4\">url</MediaFile></MediaFiles></InLine></Ad></VAST>\"",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["tag_type == VAST_Inline","critical_errors contains Impression OR tracking","is_valid == false"]
      },
      {
        "id": 2,
        "prompt": "Debug VAST: \"<?xml version=\"1.0\"?><VAST version=\"3.0\"><Ad><Wrapper><VASTAdTagURI>http://r1.com/vast.xml</VASTAdTagURI><Wrapper><VASTAdTagURI>http://r2.com/vast.xml</VASTAdTagURI></Wrapper></Wrapper></Ad></VAST>\"",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["tag_type == VAST_Wrapper","latency_warnings length > 0"]
      },
      {
        "id": 3,
        "prompt": "Debug VAST: \"<?xml version=\"1.0\"?><VAST version=\"4.0\"><Ad><InLine><Impression>http://track.com</Impression><MediaFiles><MediaFile type=\"video/mp4\">url</MediaFile></MediaFiles></InLine></Ad></VAST>\"",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["is_valid == true","critical_errors == []"]
      }
    ]
  }

Assertions:
  Eval 1: tag_type == VAST_Inline  |  critical_errors contains Impression OR tracking  |  is_valid == false
  Eval 2: tag_type == VAST_Wrapper  |  latency_warnings length > 0
  Eval 3: is_valid == true  |  critical_errors == []