# Estonian Public Procurement Register API

**Base URL:** `https://riigihanked.riik.ee/rhr/api/public/v1`
**Web UI:** https://riigihanked.riik.ee/rhr-web/#/open-data

No authentication required. Public open data.

## Monthly Notice Dumps

```
GET /opendata/notice/{year}/month/{month}/xml
```

Returns all procurement notices for that month in eForms UBL 2.3 XML format.

**Example:**
```bash
curl -O "https://riigihanked.riik.ee/rhr/api/public/v1/opendata/notice/2026/month/1/xml"
```

**Size:** ~36MB per month (January 2026)

### XML Format
- eForms SDK 1.13
- UBL 2.3 namespace
- Contains: ContractNotice, Organizations, ProcurementProject, TenderingTerms, etc.

### Key Data Fields
- `cbc:ID` — Notice UUID
- `cac:PartyName/cbc:Name` — Contracting authority name
- `cbc:CompanyID` — Registry code
- `ContractFolderID` — Links to procurement folder
- Full tender specifications and requirements

## Date Range
Historical data available. Tested working:
- 2024, 2025, 2026 notices all accessible

## Usage for Monitoring

1. **Daily/Weekly Download:** Fetch latest month's XML
2. **Parse with Python:** Use `lxml` or `xmltodict`
3. **Extract:** Notice text, CPV codes, estimated values, deadlines
4. **Analyze:** AI scan for brand restrictions, split contracts, etc.

### Sample Python Snippet
```python
import requests
from lxml import etree

url = "https://riigihanked.riik.ee/rhr/api/public/v1/opendata/notice/2026/month/1/xml"
response = requests.get(url)

root = etree.fromstring(response.content)
ns = {
    'cn': 'urn:oasis:names:specification:ubl:schema:xsd:ContractNotice-2',
    'cbc': 'urn:oasis:names:specification:ubl:schema:xsd:CommonBasicComponents-2',
    'cac': 'urn:oasis:names:specification:ubl:schema:xsd:CommonAggregateComponents-2'
}

for notice in root.findall('.//cn:ContractNotice', ns):
    notice_id = notice.find('.//cbc:ID', ns).text
    # Process each notice...
```

## Related Endpoints

- `/opendata/contract/{year}/month/{month}/xml` — Contract data (may have limited availability)
- Individual notice HTML: `/notice/{id}/html`

---

*Last verified: 2026-02-10*
