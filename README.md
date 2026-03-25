# 🌍 AI Powered Tours & Holidays Chatbot — Zoho Cliqtrix Hackathon

<p align="center">
  <img src="https://img.shields.io/badge/Built%20With-Zoho%20SalesIQ-F7C300?style=for-the-badge&logo=zoho&logoColor=black" />
  <img src="https://img.shields.io/badge/Scripting-Deluge-0052CC?style=for-the-badge" />
  <img src="https://img.shields.io/badge/AI-Google%20Gemini-4285F4?style=for-the-badge&logo=google&logoColor=white" />
  <img src="https://img.shields.io/badge/Database-Airtable-18BFFF?style=for-the-badge&logo=airtable&logoColor=white" />
  <img src="https://img.shields.io/badge/Hackathon-Zoho%20Cliqtrix-F01E00?style=for-the-badge" />
</p>

---

## 🏆 About the Competition

**Zoho Cliqtrix** is a competitive hackathon organized by Zoho, challenging developers to build innovative, real-world solutions powered entirely by Zoho technologies. Participants design, develop, and present fully functional applications under strict time constraints, showcasing creativity and technical depth within the Zoho ecosystem.

This project was built as my submission for the Zoho Cliqtrix Hackathon.

---

## 📌 Project Overview

This is an **AI-powered Tours & Holidays Chatbot** that runs inside **Zoho SalesIQ Bot Builder**, scripted in **Deluge**. The bot enables users to discover the best travel packages across India simply by entering their city or location.

The bot:
- Runs on **Zoho SalesIQ Bot Builder** using **Deluge scripting**
- Integrates with **Google Gemini AI API** for intelligent geographic understanding
- Uses **Airtable** as a structured travel package database
- Detects the user's location and identifies the nearest Indian state
- Finds the nearest matching city from the package database
- Dynamically recommends the best travel packages in real time

---

## ✨ Key Features

| Feature | Description |
|---|---|
| 🧠 **AI Location Detection** | Uses Gemini AI to validate and map user locations to Indian States/UTs |
| 📍 **Nearest City Matching** | AI selects the geographically closest city from the packages database |
| 🎒 **Dynamic Package Recommendation** | Shows real-time travel packages tailored to the user's location |
| 🗄️ **Airtable Database Integration** | Live queries to a structured Airtable travel packages database |
| 🔍 **Smart Package Filtering** | Filters by state, then narrows to city-level for precision |
| 🤖 **Interactive Chatbot Experience** | Conversational UI via Zoho SalesIQ with itinerary on demand |
| 🌏 **Foreign Location Handling** | Gracefully identifies & responds to non-Indian location queries |
| ❌ **Invalid Input Handling** | Safely handles typos, gibberish, and unknown inputs |

---

## 🛠️ Technologies Used

| Technology | Role |
|---|---|
| **Zoho SalesIQ Bot Builder** | Chatbot platform and conversation interface |
| **Deluge Script** | Server-side scripting language for all bot logic |
| **Google Gemini AI API** | AI-powered location and city inference |
| **Airtable API** | Travel package database with REST API access |
| **REST APIs** | Communication layer between all integrations |

---

## 🗄️ Database

**Airtable Travel Packages Database:**
[🔗 View Public Database](https://airtable.com/appSCe6HSDeZyheMX/shrJOvEPnbANXTmiY)

The database stores:

| Field | Description |
|---|---|
| `Package Name` | Name of the travel package |
| `Id` | Unique identifier used to retrieve itineraries |
| `Cities Covered` | Cities included in the package itinerary |
| `State` | Indian State or UT the package belongs to |
| `Package Type` | Type of trip (e.g., Hill Station, Beach, Heritage) |
| `Number of Days` | Trip duration |
| `Price per Person (INR)` | Per-person cost in Indian Rupees |
| `Itinerary` | Full day-by-day trip plan |

---

## 🔄 Project Workflow

```
User enters city/location in the chat
        ↓
Bot sends location to Google Gemini AI
        ↓
Gemini AI detects:
  ├── Valid Indian City → Returns State/UT name
  ├── Foreign Location → Returns "Foreign"
  └── Invalid Input    → Returns "Unknown"
        ↓
Bot handles gracefully:
  ├── Foreign → Friendly "India only" message
  ├── Unknown → "Try a different location" message
  └── Valid   → Continues to Airtable query
        ↓
Bot queries Airtable for packages in that State
        ↓
Gemini AI identifies the nearest matching city
        ↓
Bot fetches city-specific packages from Airtable
        ↓
Bot displays top matching packages with Package IDs
        ↓
User enters a Package ID
        ↓
Bot returns full itinerary, pricing, and inclusions
```

---

## 💡 Why This Project is Unique

- **AI-Powered Geographic Intelligence** — The bot doesn't rely on hardcoded city lists. Gemini AI dynamically interprets natural language inputs and geographically maps them to Indian States.
- **Two-Stage AI Filtering** — First the state is detected, then the nearest city is intelligently matched — a dual-AI approach for maximum accuracy.
- **Dynamic API Integrations** — All data is live. No hardcoded package data; everything is pulled in real-time from Airtable.
- **Built 100% in Zoho Deluge** — The entire logic — AI calls, Airtable queries, session management, response formatting — is written using Deluge with no external backend.
- **Edge Case Handling** — Foreign locations and invalid inputs are handled gracefully with user-friendly messages.

---

## 📦 Example Bot Interaction

**User Input:**
> Chennai

**Bot Response:**
```
Here are the available packages for your location:

*South India Coastal Explorer*

Package ID: PKG-001
Cities Covered: Chennai, Mahabalipuram, Pondicherry
Package Type: Beach & Heritage
Number of Days: 5
Price per Person (INR): ₹12,499


*Temple Trail Tamil Nadu*

Package ID: PKG-002
Cities Covered: Madurai, Rameswaram, Kanyakumari
Package Type: Pilgrimage & Culture
Number of Days: 4
Price per Person (INR): ₹9,999


Please enter the Package ID to view the itinerary.
```

**User enters:** `PKG-001`

**Bot returns the full 5-day itinerary with hotel recommendations and inclusions.**

---

## 📁 Project Structure

```
Zoho-Cliqtrix-AI-Tours-Bot/
│
├── README.md
│
├── docs/
│   ├── architecture.md        ← System architecture and data flow
│   ├── workflow.md            ← Step-by-step workflow documentation
│   └── competition.md        ← Hackathon competition context
│
├── deluge/
│   ├── main_bot_script.deluge        ← Main SalesIQ bot controller
│   ├── ai_location_detection.deluge  ← Gemini AI location detection
│   ├── package_search.deluge         ← Airtable package search logic
│   └── booking_flow.deluge           ← Package ID → Itinerary flow
│
├── integrations/
│   ├── airtable_api.md        ← Airtable setup and field mapping
│   └── gemini_ai_api.md       ← Google Gemini AI integration guide
│
├── assets/
│   └── bot_workflow_diagram.png
│
└── LICENSE
```

---

## ⚙️ Setup & Deployment

### 1. Configure API Keys

Replace the following placeholders in all `.deluge` files before deploying:

| Placeholder | Description |
|---|---|
| `{{GEMINI_API_KEY}}` | Your Google Gemini AI API Key |
| `{{AIRTABLE_API_KEY}}` | Your Airtable Personal Access Token |
| `{{AIRTABLE_BASE_ID}}` | Your Airtable Base ID |
| `{{AIRTABLE_TABLE_ID}}` | Your Airtable Table ID |

### 2. Deploy to Zoho SalesIQ

1. Open **Zoho SalesIQ → Bot Builder**
2. Create a new bot or open an existing one
3. Add **Answer Bots / Function Plugs** for each Deluge file:
   - `main_bot_script.deluge` → Main bot handler
   - `ai_location_detection.deluge` → AI location plug
   - `package_search.deluge` → Package search plug
   - `booking_flow.deluge` → Itinerary retrieval plug
4. Configure bot session variables: `city`, `packageId`
5. Connect the plugs in the correct flow sequence

---

## 👤 Author

**Niranjan**

[![GitHub](https://img.shields.io/badge/GitHub-niranjan169-181717?style=flat&logo=github)](https://github.com/niranjan169)

---

## 📄 License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
