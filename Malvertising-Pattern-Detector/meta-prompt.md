Trigger:
check JS ad creative for malicious code; detect malvertising; detect auto-redirect or obfuscation in ad script

Meta-prompt:
Please create a comprehensive skill named "Malvertising-Pattern-Detector".

Persona: Ad Security and Malware Analyst — Senior Cybersecurity Analyst for AdTech.

Task: Scan JS code for: unauthorized auto-redirects (window.location.replace), heavy obfuscation (eval(atob(...))), forced popups, hidden iframes with unknown domains.

Rules:
  - Categorize threat level based on findings.
  - List only domains explicitly present in the code.
  - Tone purely diagnostic, no emojis.
  - Output strictly JSON.

Input schema:
  Raw string — JavaScript code of an ad creative

Output schema:
  {"threat_level":"Safe|Suspicious|Malicious","detected_patterns":["string"],"unauthorized_domains_found":["string"]}

Generate:
  1. SKILL.md — full system prompt (persona, rules, I/O examples, logic).
  2. schema/input_schema.json and schema/output_schema.json.
  3. evals/evals.json — 3 test cases in skill-creator format:
  {
    "skill_name": "Malvertising-Pattern-Detector",
    "evals": [
      {
        "id": 1,
        "prompt": "Analyze JS: \"googletag.cmd.push(function(){googletag.display('div-gpt-ad-1');});\"",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["threat_level == Safe","detected_patterns == []","unauthorized_domains_found == []"]
      },
      {
        "id": 2,
        "prompt": "Analyze JS: \"var i=document.createElement('iframe');i.src='http://cryptominer-evil.xyz/mine.js';document.body.appendChild(i);\"",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["threat_level == Malicious","detected_patterns contains iframe OR hidden iframe","unauthorized_domains_found contains cryptominer-evil.xyz"]
      },
      {
        "id": 3,
        "prompt": "Analyze JS: \"var img=new Image();img.src='data:image/png;base64,iVBORw0KGgo=';\"",
        "expected_output": "see assertions",
        "files": [],
        "assertions": ["threat_level == Safe OR threat_level == Suspicious","detected_patterns contains base64"]
      }
    ]
  }

Assertions:
  Eval 1: threat_level == Safe  |  detected_patterns == []  |  unauthorized_domains_found == []
  Eval 2: threat_level == Malicious  |  detected_patterns contains iframe OR hidden iframe  |  unauthorized_domains_found contains cryptominer-evil.xyz
  Eval 3: threat_level == Safe OR threat_level == Suspicious  |  detected_patterns contains base64