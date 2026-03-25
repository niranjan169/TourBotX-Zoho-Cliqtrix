# 📋 Project Workflow

## AI Powered Tours & Holidays Chatbot — Step-by-Step Flow

---

## Overview

The bot guides users from entering a location all the way to viewing a complete travel itinerary — entirely through a conversational chatbot interface powered by AI and live database queries.

---

## Detailed Workflow

### Step 1 — User Enters Location
The user types a city, town, or place name into the Zoho SalesIQ chatbot widget.

**Example inputs:**
- `Chennai`
- `Near Ooty`
- `London` ← handled gracefully as Foreign
- `xyzabc123` ← handled gracefully as Unknown

---

### Step 2 — AI State Detection (Gemini AI — Call 1)

The bot sends the location to the **Google Gemini AI API** with a strict prompt:

> *"Analyze the following input. If it's a valid Indian location, return the State/UT name. If it's outside India, return 'Foreign'. If it's invalid, return 'Unknown'."*

**Possible AI outputs:**

| AI Response | Bot Action |
|---|---|
| `Tamil Nadu` (State/UT name) | Continue to Airtable query |
| `Foreign` | Respond: "We offer India-only packages" |
| `Unknown` | Respond: "Please try a different location" |

---

### Step 3 — Airtable State-Level Query

If the location is valid and Indian, the bot queries **Airtable** using a filter formula:

```
State = "Tamil Nadu"
```

This returns all travel packages whose State field matches the detected state.

- **If 0 records found:** The bot responds: *"No packages available for this state."*
- **If records found:** Continue to Step 4.

---

### Step 4 — Nearest City Matching (Gemini AI — Call 2)

The bot collects all city names from the returned records and sends them to Gemini AI with a second prompt:

> *"From this list of cities, return the BEST MATCH for the user's input. If an exact match exists, return it. Otherwise return the geographically closest one."*

This ensures smart matching even when the user types a nearby village or suburb rather than an exact city in the database.

---

### Step 5 — Airtable City-Level Query

The bot uses Airtable's FIND formula to fetch packages specifically for the nearest city:

```
FIND("Chennai", {Cities Covered})
```

- **If 2+ city-specific packages found:** Show those
- **If fewer than 2 found:** Fallback to state-level packages

---

### Step 6 — Display Package List

The bot returns a formatted list of up to **2 matching packages**, each showing:

- 📦 Package Name
- 🔑 Package ID
- 📍 Cities Covered
- 🏷️ Package Type
- 🗓️ Number of Days
- 💰 Price per Person (INR)

The user is prompted to enter a **Package ID** to view the itinerary.

---

### Step 7 — User Enters Package ID

The user sends back the Package ID (e.g., `PKG-001`).

---

### Step 8 — Itinerary Retrieval

The bot queries Airtable for the exact record matching that Package ID and returns:

- Full day-by-day itinerary
- Hotel recommendations (if stored)
- Inclusions and exclusions
- Final pricing confirmation

---

## Flow Diagram

```
User enters location
      ↓
[Gemini AI] Detect State / UT
      ↓
  ┌───┴───┐
  │       │
Foreign  Unknown
  │       │
  ▼       ▼
Error   Error
message message
  │
  ▼
[Airtable] Fetch state packages
      ↓
[Gemini AI] Find nearest city
      ↓
[Airtable] Fetch city packages (fallback: state)
      ↓
Display package list (max 2)
      ↓
User enters Package ID
      ↓
[Airtable] Fetch itinerary by ID
      ↓
Display full itinerary
```

---

## Error Handling

| Scenario | Bot Response |
|---|---|
| Gemini API failure | Defaults to "Unknown" → friendly error message |
| No packages in state | "No packages available for this state" |
| No packages in city | Falls back to state-level packages |
| Invalid Package ID | "No package found for this ID, please check and retry" |
| Foreign location | "We currently offer India-only packages" |
| Typographical errors | Gemini AI handles common misspellings intelligently |
