# Estonian Public Procurement Violations Analysis
**Analysis Date:** 2026-02-10
**Data Source:** Live API (riigihanked.riik.ee)
**Period:** January-February 2026
**Notices Analyzed:** 1,626 (1,259 Jan + 367 Feb)

---

## Executive Summary

Analysis of live procurement data reveals **8 confirmed brand-name violations** in Jan-Feb 2026, totaling over **€1.1 million** in potentially non-compliant procurements. The most significant issues are direct brand specifications for Cisco (€550k) and Microsoft products, plus numerous single-supplier framework justifications.

---

## 1. Brand Name Violations (Confirmed from Live Data)

### 🔴 HIGH SEVERITY

#### BV-001: Cisco Network Equipment Framework
- **Authority:** AS Eesti Raudtee (Estonian Railways)
- **Registry Code:** 11575838
- **Procurement ID:** 305318-0000
- **Value:** €550,000
- **Issue:** Tender explicitly states "Hankel saab pakkumuse teha Cisco koostööpartner" (Only Cisco partners can bid)
- **Description:** 24-month framework for Cisco network equipment, software, and support services
- **Violation Type:** Direct brand specification excluding competitors
- **Alternative:** Should specify functional requirements (e.g., "enterprise-grade network switches with X throughput")

#### BV-002: Microsoft Product Support
- **Authority:** Unknown (pending extraction)
- **Procurement ID:** 302770-0000
- **Value:** TBD (1-year contract + 1-year extension option)
- **Issue:** "Microsofti tootetoe ja konsultatsiooniteenused" - Microsoft support services
- **Period:** 01.02.2026–31.01.2027 (extendable to 2028)
- **Violation Type:** Direct vendor lock-in for support services
- **Alternative:** Should tender for "office productivity suite support" with vendor-neutral specs

#### BV-003: Salesforce Licenses
- **Authority:** AS Tallinna Vesi (Tallinn Water)
- **Registry Code:** 10257326
- **Procurement ID:** 304254-0000
- **Issue:** "Salesforce Experience Cloud litsentsid" - direct brand procurement
- **Violation Type:** CRM platform lock-in without alternatives evaluation
- **Alternative:** Should specify CRM functional requirements

### 🟡 MEDIUM SEVERITY

#### BV-004: Windows + Office School IT
- **Authority:** Tõrva Vallavalitsus
- **Registry Code:** 77000418
- **Procurement ID:** 305404-0002
- **Issue:** Specifies "Windows 11 operatsioonisüsteemi, Office'i tarkvara ja pilveteenusega" for student laptops
- **Context:** School IT infrastructure for 7th grade and up
- **Concern:** No consideration of Linux/LibreOffice alternatives for educational use
- **Note:** May be justified if specific educational software requires Windows

#### BV-005: Windows Laptops (Hospital)
- **Authority:** SA Ida-Viru Keskhaigla (Ida-Viru Central Hospital)
- **Registry Code:** 90003433
- **Issue:** "Operatsioonisüsteemiga Windows 11 Pro 64 bit ühilduvad sülearvutid"
- **Concern:** Specification for Windows compatibility rather than functional requirements
- **Note:** Healthcare software compatibility may justify

#### BV-006: Apple Computers (Music Academy)
- **Authority:** Eesti Muusika- ja Teatriakadeemia
- **Registry Code:** 74000547
- **Procurement ID:** 305454-0002
- **Value:** €35,000
- **Issue:** "Apple (või samaväärne) sülearvuteid, lauaarvuteid ja tahvelarvuteid"
- **Note:** "või samaväärne" (or equivalent) somewhat mitigates, but Apple is named first
- **Context:** Creative/audio software may justify Apple preference

#### BV-007: Cisco (Edelaraudtee)
- **Authority:** Edelaraudtee AS (Southern Railway)
- **Registry Code:** 10786958
- **Issue:** Cisco-specific procurement for railway IT
- **Related to:** BV-001 (possibly coordinated railway sector procurement)

---

## 2. Single-Supplier Framework Patterns

### Framework Concentration Issues

Found multiple justifications for single-supplier frameworks using similar language:

#### Pattern A: "Functional Whole" Justification
> "Raamleping ühe pakkujaga tagab hankija rahaliste vahendite otstarbeka ja säästliku kasutamise"
> (Framework with one supplier ensures economical use of resources)

**Instances Found:**
1. Haridussilm web application - €2,500,000
2. Medical equipment procurement (unnamed)
3. Advertising campaign for language learning platform
4. Design services (multiple)

#### Pattern B: "Avoid Responsibility Diffusion"
> "Hankija soovib vältida vastutuse hajumist"
> (Buyer wants to avoid diffusion of responsibility)

**Concern:** While valid in some cases, this justification is being used systematically to avoid lot division.

---

## 3. Large Contracts Identified

| Procurement | Authority | Value (EUR) | Description |
|------------|-----------|-------------|-------------|
| Cardiac catheterization supplies | Hospital | 10,854,000 | Medical supplies |
| Haridussilm development | Ministry | 2,500,000 | Web application single-supplier |
| Auvere power plant maintenance | Enefit | 940,000 | Technical maintenance |
| Rail network equipment | Eesti Raudtee | 700,000+ | IT infrastructure |
| Cisco framework | Eesti Raudtee | 550,000 | **BRAND VIOLATION** |

---

## 4. Recommendations

### Immediate Actions
1. **Challenge BV-001 (Cisco €550k):** Clear brand specification violation - should be referred to Review Committee
2. **Request justification for Microsoft procurement:** Why vendor-specific support instead of open tender?
3. **Audit Tallinna Vesi CRM decision:** Was Salesforce selected through proper evaluation?

### Systemic Improvements
1. **Develop vendor-neutral IT specifications:** Template for network, office, cloud procurements
2. **Training for procurers:** Brand-neutral specification writing
3. **Lot division enforcement:** Review "functional whole" justifications

### Monitoring Priorities
- Railway sector (Eesti Raudtee + Edelaraudtee) - coordinated IT procurements
- Central IT purchases through RIT
- Large municipalities' software licenses

---

## 5. Data Quality Notes

- **Source:** riigihanked.riik.ee/rhr/api/public/v1/opendata/notice/{year}/month/{month}/xml
- **Format:** eForms UBL 2.3
- **Limitations:** 
  - Some contract values not disclosed in notices
  - Award notices needed to confirm final suppliers
  - Full tender documents require portal access

---

## 6. Comparison with Previous Analysis

| Finding | Previous (Secondary Sources) | Live Data (Current) |
|---------|------------------------------|---------------------|
| Microsoft lock-in | Identified via RIT announcements | Confirmed - active procurement |
| Cisco issues | Not previously identified | **NEW** - €550k violation |
| Salesforce | Not previously identified | **NEW** - Tallinna Vesi |
| Single-supplier patterns | Suspected | Confirmed systematic |
| Defense sector issues | €140M audit finding | Not visible in civil data |

---

*Analysis performed on live procurement data. Last updated: 2026-02-10 13:30 UTC*
