# Estonian Procurement Monitor - Claude Code Spec

## Project Goal
Build a procurement monitoring tool that automatically detects potential irregularities in Estonian public procurements, presenting findings in a clean web dashboard.

## Data Source

### Estonian Procurement Registry API
- **Endpoint:** `https://riigihanked.riik.ee/rhr/api/public/v1/opendata/notice/{year}/month/{month}/xml`
- **Format:** XML (eForms UBL standard)
- **Auth:** None required
- **Size:** ~35MB/month (Jan 2026 had 1,218 notices)
- **Update frequency:** Monthly dumps

### Procurement Registry URLs
- **Pattern:** `https://riigihanked.riik.ee/rhr-web/#/procurement/{numeric_id}/general-info`
- **ID location in XML:** Look for `<cbc:URI>` containing `/procurement/{id}/`

## XML Structure (Key Fields)

```xml
<ContractNotice>
  <!-- Procurement ID in URI -->
  <cbc:URI>https://riigihanked.riik.ee/rhr-web/#/procurement/9843504/...</cbc:URI>
  
  <!-- Buyer (ORG-0001 is actual buyer, ORG-0002/0003 are standard entities) -->
  <cbc:ID schemeName="organization">ORG-0001</cbc:ID>
  <cac:PartyName>
    <cbc:Name>AS Eesti Raudtee</cbc:Name>
  </cac:PartyName>
  
  <!-- Procurement details in ProcurementProject -->
  <cac:ProcurementProject>
    <cbc:Name>Cisco toodete raamleping</cbc:Name>
    <cbc:Description>Raamlepingu sõlmimine 24 kuuks...</cbc:Description>
    <cbc:Note>Hange ei ole jaotatud osadeks...</cbc:Note>
  </cac:ProcurementProject>
  
  <!-- Value (not always present) -->
  <efbc:EstimatedOverallContractAmount>550000.00</efbc:EstimatedOverallContractAmount>
  
  <!-- Procedure type -->
  <cbc:ProcedureCode listName="procurement-procedure-type">open</cbc:ProcedureCode>
  
  <!-- Submission deadline -->
  <cac:TenderSubmissionDeadlinePeriod>
    <cbc:EndDate>2026-02-27+02:00</cbc:EndDate>
  </cac:TenderSubmissionDeadlinePeriod>
  
  <!-- CPV codes -->
  <cbc:ItemClassificationCode listName="cpv">48219000</cbc:ItemClassificationCode>
</ContractNotice>
```

## Analytical Frames

### 1. Brand Name Violations (HIGH PRIORITY)
**Legal basis:** RHS § 88 lg 5, EU Directive 2014/24 Art 42

**Detection:**
- Search title + description for brand names using WHOLE WORD matching: `\bCisco\b`, `\bMicrosoft\b`, etc.
- Check if `<cbc:Note>` contains "samaväärne" (Estonian for "or equivalent")
- Violation = brand named WITHOUT "samaväärne" clause

**Brands to detect:** Cisco, Microsoft, Apple, Salesforce, Oracle, SAP, IBM, Lenovo, VMware, Adobe, Windows, Office

**Known violations found (Feb 2026):**
- Eesti Raudtee: "Cisco toodete raamleping" €550k - explicitly limits to Cisco partners
- Edelaraudtee: "IKT võrguseadmete vahetus" - same issue

**CRITICAL:** Avoid false positives! "SAP" matches Estonian word "osapoolte" (stakeholders). Always use `\bSAP\b` word boundary regex.

### 2. Threshold Clustering
**Legal basis:** RHS § 14 (prohibition on artificial splitting)

**Detection:**
- Flag values suspiciously close to thresholds: €29,000-30,000, €58,000-60,000, €140,000-143,000
- Especially suspicious: exact values like €29,999

**Thresholds:**
- €30,000: Below = simplified procedure
- €60,000: Below = national only
- €143,000: Below = no EU-wide publication required

### 3. Lot Avoidance
**Legal basis:** RHS § 27 (obligation to divide into lots for SME access)

**Detection:**
- Find `<cbc:Note>` containing "ei ole jaotatud osadeks" or "ei jaga"
- Extract justification text
- High-value contracts without lots deserve scrutiny

### 4. Short Deadlines
**Detection:**
- Calculate days between notice publication and `<cbc:EndDate>` in TenderSubmissionDeadlinePeriod
- Flag if ≤7 days (limits competition)
- Standard minimum is 30-35 days for open procedures

### 5. Procedure Type Distribution
**Detection:**
- Extract `<cbc:ProcedureCode>` values
- Flag buyers with high proportion of non-open procedures
- Procedure codes: `open`, `oth-single`, `neg-w-call`, `restricted`

### 6. Buyer Concentration
**Detection:**
- Count notices per buyer
- Flag if single buyer dominates (e.g., Elektrilevi had 12% of all notices)
- Track top 10 buyer share

### 7. Framework Agreement Overuse
**Detection:**
- Check `<cbc:ContractingSystemTypeCode listName="framework-agreement">`
- Flag buyers where >80% of notices are framework agreements
- Found: Hospitals use frameworks for almost everything

## Output Requirements

### Web Dashboard
- **Theme:** Light, clean, professional
- **Sections:** Collapsible by default
- **For each finding:** 
  - Title, buyer, value
  - Expandable analysis (what's wrong + what should be done)
  - Direct link to registry
- **Stats bar:** Total analyzed, violations by category

### Data Export
- JSON with all findings
- Include procurement IDs for registry lookup

## Technical Architecture (Recommended)

```
/estonia-procurement-monitor
├── backend/
│   ├── fetch.py          # Download monthly XML
│   ├── parse.py          # Extract structured data
│   ├── analyze.py        # Apply analytical frames
│   └── data/             # Store processed JSONs
├── frontend/
│   ├── index.html        # Single-page dashboard
│   ├── styles.css        # Light theme
│   └── app.js            # Load data, render sections
└── README.md
```

**Hosting options:**
- GitHub Pages (static frontend + pre-processed data)
- Vercel/Railway (if backend automation needed)

## Lessons Learned

1. **Regex matters:** Use word boundaries (`\b`) to avoid false positives
2. **ORG-0001 is buyer:** ORG-0002/0003 are always "Riigihangete vaidlustuskomisjon" and "Riigihangete register"
3. **Values often missing:** Many notices don't have EstimatedOverallContractAmount
4. **Numeric IDs for URLs:** Registry uses numeric IDs (e.g., 9843504), not UUIDs
5. **"Samaväärne" = compliant:** If they added "or equivalent", it's technically legal (even if practically limiting)

## Sample Verified Violations

```json
[
  {
    "id": "9843504",
    "title": "Cisco toodete raamleping",
    "org": "AS Eesti Raudtee",
    "brands": ["Cisco"],
    "has_equiv": false,
    "value": 550000,
    "problem": "Title explicitly names Cisco. Documentation states only Cisco partners may bid.",
    "should_be": "Network equipment framework with functional requirements"
  },
  {
    "id": "9830964",
    "title": "IKT võrgseadmete vahetus",
    "org": "Edelaraudtee AS",
    "brands": ["Cisco"],
    "has_equiv": false,
    "value": null,
    "problem": "References Cisco equipment without 'or equivalent' clause",
    "should_be": "Network equipment replacement with technical specifications"
  }
]
```

## Future Extensions

- **Repeat winner analysis:** Track which companies win multiple contracts from same buyer
- **Price benchmarking:** Compare similar procurements across buyers
- **Timeline tracking:** Monitor for post-award amendments
- **Cross-country comparison:** Apply same framework to other EU countries (TED data)

## Resources

- Estonian Procurement Registry: https://riigihanked.riik.ee
- RHS (Procurement Law): https://www.riigiteataja.ee/akt/101072017001
- EU Directive 2014/24: https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=celex%3A32014L0024
- Downloaded XML samples: `/tmp/notices_2026_01.xml`, `/tmp/notices_2026_02.xml`
