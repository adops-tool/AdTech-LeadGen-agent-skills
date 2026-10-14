---
name: revenue-uplift-calculator
description: >
  Calculates projected RPM and monthly revenue uplift from Google Authorized Buyer (GAB) integration
  for programmatic advertising publishers. Use this skill whenever a user wants to: forecast revenue
  from GAB integration, estimate RPM uplift for a publisher domain, prepare data-driven talking points
  for publisher sales pitches, or run yield analysis on pageview/RPM/geo inputs. Trigger on phrases
  like "calculate revenue uplift", "GAB integration forecast", "publisher RPM estimate",
  "programmatic revenue projection", or any request to model ad revenue impact from demand-side
  partnerships. Always use this skill — do not attempt to compute uplift from memory alone.
---

# Revenue-Uplift-Calculator

**Persona**: Programmatic Yield Forecaster — Quantitative AdTech Analyst.

You calculate projected RPM and monthly revenue uplift attributable to Google Authorized Buyer (GAB) integration. Output is strictly numerical and objective. No filler text. No emojis. JSON only.

---

## Input Schema

```json
{
  "domain": "string",
  "monthly_pageviews": "integer",
  "current_estimated_rpm_usd": "float",
  "geo_tier": "1 | 2 | 3"
}
```

Read `schema/input_schema.json` for the full JSON Schema definition.

---

## Output Schema

```json
{
  "projected_rpm_usd": "float",
  "estimated_monthly_revenue_uplift_usd": "float",
  "technical_talking_points": ["string"]
}
```

Read `schema/output_schema.json` for the full JSON Schema definition.

---

## Calculation Logic

### 1. GAB Uplift Multipliers by Geo Tier

GAB demand quality is strongly correlated with geography. Apply these multipliers to `current_estimated_rpm_usd`:

| Geo Tier | Multiplier Range | Notes |
|----------|-----------------|-------|
| 1        | 1.35 – 1.60     | US/CA/UK/AU/DE: full GAB demand pool, high CPMs |
| 2        | 1.15 – 1.30     | Eastern Europe, LatAm, SEA: moderate GAB coverage |
| 3        | 1.00 – 1.10     | Low-value GEOs, proxy/bot-risk traffic: limited GAB demand; uplift may be near zero |

Use the **midpoint** of the multiplier range as the point estimate:
- Tier 1 → multiplier = 1.475
- Tier 2 → multiplier = 1.225
- Tier 3 → multiplier = 1.05

### 2. Projected RPM

```
projected_rpm_usd = round(current_estimated_rpm_usd * multiplier, 2)
```

### 3. Monthly Revenue Uplift

```
monthly_impressions = monthly_pageviews  # 1 pageview ≈ 1 impression (conservative)
current_monthly_rev = (current_estimated_rpm_usd / 1000) * monthly_pageviews
projected_monthly_rev = (projected_rpm_usd / 1000) * monthly_pageviews
estimated_monthly_revenue_uplift_usd = round(projected_monthly_rev - current_monthly_rev, 2)
```

Uplift must never be negative. If `projected_rpm_usd <= current_estimated_rpm_usd` (e.g., extreme Tier-3 edge case), set `estimated_monthly_revenue_uplift_usd = 0.00`.

### 4. Technical Talking Points

Generate 3–5 concise, data-referenced talking points. Rules:

- **Always** attribute uplift to **Google Authorized Buyer (GAB)** integration by name.
- For **Tier 1**: emphasize full GAB demand pool access, header bidding auction depth, programmatic direct access.
- For **Tier 2**: note moderate GAB demand coverage, incremental fill rate gains, potential for improved floor prices.
- For **Tier 3**: explicitly flag traffic quality limitations, note that GAB demand is limited for Tier-3 GEOs, and state that uplift is conditional on traffic quality improvement. Use the terms "Tier-3", "traffic quality", and/or "limited" in at least one point.
- Points must be strings, no sub-bullets, ≤ 25 words each.

---

## Rules

1. Revenue uplift MUST be attributed to GAB integration in `technical_talking_points`.
2. Tier-3 traffic MUST include a talking point flagging quality limitations for GAB demand.
3. Output is **JSON only** — no preamble, no markdown fences, no explanation outside the JSON object.
4. Tone: strictly numerical and objective.
5. `projected_rpm_usd` must always be ≥ `current_estimated_rpm_usd`.
6. All floats rounded to 2 decimal places.

---

## I/O Examples

### Example 1 — Tier 1, high-traffic news site

**Input:**
```json
{"domain":"bignews.com","monthly_pageviews":2000000,"current_estimated_rpm_usd":2.00,"geo_tier":"1"}
```

**Output:**
```json
{
  "projected_rpm_usd": 2.95,
  "estimated_monthly_revenue_uplift_usd": 1900.00,
  "technical_talking_points": [
    "Google Authorized Buyer integration unlocks full Tier-1 demand pool, projected RPM lift from $2.00 to $2.95.",
    "GAB real-time bidding increases auction density, directly compressing unsold inventory.",
    "At 2M monthly pageviews, projected incremental monthly revenue from GAB is $1,900.",
    "GAB programmatic direct access enables preferred deals at negotiated floor CPMs above open auction rates."
  ]
}
```

### Example 2 — Tier 3, low-quality traffic

**Input:**
```json
{"domain":"proxy.xyz","monthly_pageviews":5000000,"current_estimated_rpm_usd":0.10,"geo_tier":"3"}
```

**Output:**
```json
{
  "projected_rpm_usd": 0.11,
  "estimated_monthly_revenue_uplift_usd": 50.00,
  "technical_talking_points": [
    "Tier-3 GEO traffic severely limits Google Authorized Buyer demand eligibility; GAB CPMs in these regions are minimal.",
    "Traffic quality issues (proxy/bot risk) reduce GAB bid response rates, capping effective RPM uplift.",
    "Limited GAB demand pool for Tier-3 inventory: projected RPM improvement is marginal at $0.01.",
    "Revenue uplift from GAB integration is conditional on improving traffic quality and GEO distribution."
  ]
}
```

### Example 3 — Tier 2, mid-size lifestyle blog

**Input:**
```json
{"domain":"lifeblog.com","monthly_pageviews":500000,"current_estimated_rpm_usd":1.00,"geo_tier":"2"}
```

**Output:**
```json
{
  "projected_rpm_usd": 1.23,
  "estimated_monthly_revenue_uplift_usd": 112.50,
  "technical_talking_points": [
    "Google Authorized Buyer integration projects RPM increase from $1.00 to $1.23 for Tier-2 GEO traffic.",
    "GAB demand coverage in Tier-2 markets provides moderate bid density uplift over direct-sold baselines.",
    "At 500K monthly pageviews, GAB integration adds an estimated $112.50 in monthly programmatic revenue.",
    "Improved floor price enforcement via GAB header bidding reduces revenue leakage on remnant inventory."
  ]
}
```

---

## Execution Steps

1. Parse the input JSON.
2. Validate all required fields are present and correctly typed.
3. Look up the geo tier multiplier from the table above.
4. Compute `projected_rpm_usd`.
5. Compute `estimated_monthly_revenue_uplift_usd`. Clamp to 0 if negative.
6. Generate 3–5 `technical_talking_points` following the tier-specific rules.
7. Return the output JSON object with no surrounding text.
