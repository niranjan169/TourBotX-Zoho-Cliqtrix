# 🗄️ Airtable API Integration

## Overview

Airtable is used as the travel packages database for this project. All package discovery, filtering, and itinerary retrieval is powered by live Airtable REST API calls from Deluge scripts.

**Public Database View:**
[🔗 View Airtable Database](https://airtable.com/appSCe6HSDeZyheMX/shrJOvEPnbANXTmiY)

---

## Database Schema

| Field Name | Type | Description |
|---|---|---|
| `Id` | Text | Unique Package ID (e.g., `PKG-001`) |
| `Package Name` | Text | Name of the travel package |
| `State` | Text | Indian State or UT the package belongs to |
| `Cities Covered` | Text | Comma-separated list of cities in the package |
| `Package Type` | Text | Category (Beach, Hill Station, Heritage, etc.) |
| `Number of Days` | Number | Duration of the trip |
| `Price per Person (INR)` | Currency | Per-person cost in Indian Rupees |
| `Itinerary` | Long Text | Full day-by-day trip plan |
| `Inclusions` | Long Text | What is included in the package (optional) |
| `Exclusions` | Long Text | What is not included (optional) |

---

## API Endpoints Used

### 1. Filter by State

Used to fetch all packages in a detected Indian State.

```
GET https://api.airtable.com/v0/{baseId}/{tableId}?filterByFormula=State="Tamil Nadu"
```

**Headers:**
```
Authorization: Bearer {{AIRTABLE_API_KEY}}
```

**Example Deluge call:**
```
formula = "State=\"" + stateName + "\"";
encodedFormula = encodeUrl(formula);
url = "https://api.airtable.com/v0/" + baseId + "/" + tableId + "?filterByFormula=" + encodedFormula;

headers = Map();
headers.put("Authorization", "Bearer " + AIRTABLE_API_KEY);

apiResponse = invokeurl [ url : url  type : GET  headers : headers ];
```

---

### 2. Filter by City (FIND formula)

Used to find packages that include a specific city in their `Cities Covered` field.

```
GET https://api.airtable.com/v0/{baseId}/{tableId}?filterByFormula=FIND("Chennai", {Cities Covered})
```

**Example Deluge call:**
```
cityFormula = "FIND(\"" + nearestCity + "\", {Cities Covered})";
encodedCityFormula = encodeUrl(cityFormula);
url2 = "https://api.airtable.com/v0/" + baseId + "/" + tableId + "?filterByFormula=" + encodedCityFormula;
```

---

### 3. Filter by Package ID

Used to fetch the full itinerary for a specific package.

```
GET https://api.airtable.com/v0/{baseId}/{tableId}?filterByFormula=Id="PKG-001"
```

---

## Setup Instructions

### Step 1 — Create an Airtable Account
Sign up at [airtable.com](https://airtable.com)

### Step 2 — Create a Personal Access Token
1. Go to: [airtable.com/create/tokens](https://airtable.com/create/tokens)
2. Create a new token with **data.records:read** scope
3. Scope it to your specific base for security
4. Copy the token — it will only be shown once

### Step 3 — Get Your Base ID and Table ID
- **Base ID:** Found in the URL: `airtable.com/{baseId}/...`
- **Table ID:** Found in the URL path after the base ID

### Step 4 — Configure Placeholders
Replace in your Deluge scripts:
```
AIRTABLE_API_KEY  → your Personal Access Token
AIRTABLE_BASE_ID  → your Base ID (e.g., appXXXXXXXXXXXXXX)
AIRTABLE_TABLE_ID → your Table ID (e.g., tblXXXXXXXXXXXXXX)
```

---

## Response Structure

Airtable returns records in this JSON structure:

```json
{
  "records": [
    {
      "id": "recXXXXXXXXXXXXXX",
      "fields": {
        "Id": "PKG-001",
        "Package Name": "South India Coastal Explorer",
        "State": "Tamil Nadu",
        "Cities Covered": "Chennai, Mahabalipuram, Pondicherry",
        "Package Type": "Beach & Heritage",
        "Number of Days": 5,
        "Price per Person (INR)": 12499,
        "Itinerary": "Day 1: Arrive Chennai..."
      }
    }
  ]
}
```

In Deluge, fields are accessed as:
```
fields = rec.get("fields");
fields.get("Package Name");
fields.get("Price per Person (INR)");
```
