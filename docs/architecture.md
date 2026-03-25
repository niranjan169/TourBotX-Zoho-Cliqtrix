# 🏗️ System Architecture

## AI Powered Tours & Holidays Chatbot — Zoho Cliqtrix Hackathon

---

## Architecture Overview

The chatbot is built as a multi-layer system using Zoho SalesIQ as the frontend interface, Deluge as the scripting engine, Google Gemini AI for geographic intelligence, and Airtable as the travel packages database.

```
┌─────────────────────────────────────────────────────────┐
│                        USER                              │
│         (Types city name in the SalesIQ chatbot)        │
└────────────────────────┬────────────────────────────────┘
                         │ Chat Message
                         ▼
┌─────────────────────────────────────────────────────────┐
│               ZOHO SALESIQ BOT BUILDER                  │
│   - Hosts the conversational interface                  │
│   - Manages user sessions and context variables         │
│   - Routes user input to bot plugs (Deluge functions)   │
└────────────────────────┬────────────────────────────────┘
                         │ Triggers Deluge Script
                         ▼
┌─────────────────────────────────────────────────────────┐
│                 DELUGE SCRIPTING ENGINE                  │
│   ┌─────────────────────────────────────────────────┐  │
│   │            main_bot_script.deluge               │  │
│   │  - Reads city from user session                 │  │
│   │  - Calls ai_location_detection                  │  │
│   │  - Routes based on location type                │  │
│   │  - Calls package_search                         │  │
│   │  - Returns formatted result to bot              │  │
│   └──────────┬──────────────────┬───────────────────┘  │
│              │ AI Call           │ Data Fetch            │
│              ▼                   ▼                       │
│   ┌──────────────────┐  ┌─────────────────────────┐    │
│   │ ai_location_     │  │  package_search.deluge  │    │
│   │ detection.deluge │  │  - Airtable state query │    │
│   │ - Gemini AI call │  │  - Gemini nearest city  │    │
│   │ - Returns state  │  │  - Airtable city query  │    │
│   └──────────────────┘  └─────────────────────────┘    │
│                                                          │
│   ┌─────────────────────────────────────────────────┐  │
│   │             booking_flow.deluge                  │  │
│   │  - Accepts Package ID from user                 │  │
│   │  - Queries Airtable for itinerary               │  │
│   │  - Returns formatted trip details               │  │
│   └─────────────────────────────────────────────────┘  │
└─────────────┬───────────────────────┬───────────────────┘
              │ HTTPS POST             │ HTTPS GET / POST
              ▼                        ▼
┌─────────────────────┐     ┌────────────────────────────┐
│  GOOGLE GEMINI AI   │     │     AIRTABLE REST API      │
│  (gemini-2.5-flash) │     │                            │
│  - Detects Indian   │     │  - Stores travel packages  │
│    State/UT for     │     │  - Filters by State        │
│    a given city     │     │  - Filters by City         │
│  - Finds nearest    │     │  - Returns package details │
│    city from list   │     │  - Returns itineraries     │
└─────────────────────┘     └────────────────────────────┘
              │                        │
              └─────────┬──────────────┘
                        ▼
┌─────────────────────────────────────────────────────────┐
│              RESPONSE TO USER                           │
│   - Formatted package list with Package IDs             │
│   - Full itinerary on Package ID input                 │
│   - Graceful error messages for invalid inputs          │
└─────────────────────────────────────────────────────────┘
```

---

## Component Breakdown

### 1. Zoho SalesIQ Bot Builder
- Provides the customer-facing chat widget embedded on a website
- Manages bot flows, quick replies, and user session context
- Triggers Deluge functions (plugs) at each step of the conversation

### 2. Deluge Scripting Engine
Core business logic resides in four `.deluge` files:

| File | Responsibility |
|---|---|
| `main_bot_script.deluge` | Central orchestrator — reads input, calls AI, fetches packages, formats response |
| `ai_location_detection.deluge` | Sends city to Gemini AI, returns State/UT or validation flags |
| `package_search.deluge` | Queries Airtable by state, uses AI for nearest city, returns package list |
| `booking_flow.deluge` | Accepts Package ID, retrieves and formats full itinerary from Airtable |

### 3. Google Gemini AI API
- **Model Used:** `gemini-2.5-flash` (fast, cost-efficient)
- **Call 1 — State Detection:** Maps any Indian city/village to its State or Union Territory
- **Call 2 — Nearest City Match:** From a list of cities in the database, determines which is closest to the user's input
- Returns plain-text results, stripped of markdown by the Deluge scripts

### 4. Airtable REST API
- Serves as the travel package database
- Two query patterns:
  - **Filter by State:** `State="Tamil Nadu"` — returns all packages in a state
  - **Filter by City (FIND):** `FIND("Chennai", {Cities Covered})` — returns city-specific packages
- All data is live and can be updated without touching the Deluge code

---

## Data Flow Summary

```
User Input (city) 
  → Gemini AI (state detection)
    → Airtable (state-level packages)
      → Gemini AI (nearest city match)
        → Airtable (city-specific packages)
          → Formatted package list to user
            → User enters Package ID
              → Airtable (itinerary fetch)
                → Full itinerary to user
```

---

## Security Considerations

- All API keys are stored as **Zoho SalesIQ connection variables** or **environment-level secrets** — never hardcoded in the scripts
- Placeholders (`{{GEMINI_API_KEY}}`, `{{AIRTABLE_API_KEY}}`, etc.) are used in the repository; replace before deployment
- Airtable is accessed via a **Personal Access Token** with read-only scope on the packages table
