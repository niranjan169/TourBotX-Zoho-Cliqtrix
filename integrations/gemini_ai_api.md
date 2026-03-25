# 🤖 Google Gemini AI Integration

## Overview

Google Gemini AI is used in two critical stages of the bot's logic:

1. **State Detection** — Identifies the Indian State or Union Territory for any city entered by the user
2. **Nearest City Matching** — Determines which city from the Airtable database is closest to the user's input

Both calls use the **Gemini 2.5 Flash** model via the REST API, invoked directly from Deluge using `invokeurl`.

---

## API Details

| Property | Value |
|---|---|
| **Endpoint** | `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent` |
| **Method** | POST |
| **Auth** | API Key as query parameter |
| **Content-Type** | `application/json` |
| **Model** | `gemini-2.5-flash` |

---

## Setup Instructions

### Step 1 — Get a Gemini API Key
1. Go to [Google AI Studio](https://aistudio.google.com/app/apikey)
2. Sign in with your Google account
3. Click **Create API Key**
4. Copy the key — keep it secret

### Step 2 — Configure in Deluge
Replace the placeholder in all `.deluge` files:
```
GEMINI_API_KEY = "{{GEMINI_API_KEY}}";
```

> ⚠️ **Never commit your actual API key to a public repository.** Use Zoho SalesIQ's connection variables or environment settings to store it securely.

---

## Prompt 1 — State Detection

**Purpose:** Validate location input and identify which Indian State/UT the city belongs to.

**Prompt:**
```
Analyze the following input: '{cityName}'. STRICT RULES:
(1) If this is a valid city or place in India, return EXACTLY the State/UT name.
(2) If this is a valid location outside India, return EXACTLY 'Foreign'.
(3) If this is NOT a valid location anywhere, return EXACTLY 'Unknown'.
Return ONLY the result: State/UT name, 'Foreign', or 'Unknown'. No extra text, no explanation.
```

**Expected Outputs:**

| Input | AI Output |
|---|---|
| `Chennai` | `Tamil Nadu` |
| `Jaipur` | `Rajasthan` |
| `London` | `Foreign` |
| `xyzabc` | `Unknown` |

---

## Prompt 2 — Nearest City Matching

**Purpose:** From a list of cities in the Airtable database (for a given state), find which one is closest to the user's entered city.

**Prompt:**
```
From this list of Indian cities: {allCities}
return the BEST MATCH city for: {cityName}.
RULES:
(1) If the exact same city exists, return EXACTLY that name.
(2) Otherwise, return the geographically closest city.
Return ONLY the city name.
```

**Example:**

| User Input | Cities in DB | AI Response |
|---|---|---|
| `Near Ooty` | `Ooty, Coimbatore, Mysore` | `Ooty` |
| `Kovalam` | `Trivandrum, Kochi, Thrissur` | `Trivandrum` |

---

## Deluge API Call Structure

```
// Build request body
body = Map();
contents = List();
content = Map();
parts = List();
part = Map();
part.put("text", escaped);
parts.add(part);
content.put("parts", parts);
contents.add(content);
body.put("contents", contents);

// Make the API call
api_res = invokeurl
[
    url     : Aiurl
    type    : POST
    body    : body.toString()
    headers : {"Content-Type": "application/json"}
];

// Extract the text response
output = api_res.get("candidates").get(0).get("content").get("parts").get(0).get("text");
```

---

## Output Cleanup

Gemini sometimes returns markdown symbols in its output. These are stripped in Deluge:

```
output = output.replaceAll("", "")
               .replaceAll("\\*", "")
               .replaceAll("#", "")
               .trim();
```

---

## Why Gemini 2.5 Flash?

| Reason | Detail |
|---|---|
| **Speed** | Optimized for low-latency responses — ideal for real-time chatbot use |
| **Geographic Knowledge** | Excellent at mapping Indian cities to their states |
| **Strict Prompt Following** | Reliably returns plain-text single-word or short answers |
| **Cost Efficiency** | Flash tier is significantly cheaper than Pro, suitable for high-volume bot usage |
