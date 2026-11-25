[task_sheet_8_1_1_readme (1).md](https://github.com/user-attachments/files/35384730/task_sheet_8_1_1_readme.1.md)
# Task Sheet 8.1.1: Building an API-Connected AI Chatbot

## Overview
This repository contains the laboratory instructions, architecture workflow, and grading rubric for **Task Sheet 8.1.1**. In this hands-on activity, students build an end-to-end AI chatbot powered by an external REST API, automated webhook pipelines, structured knowledge tables, and post-conversation prompt logging.

---

## Learning Objectives
By completing **Task Sheet 8.1.1**, students will be able to:
1. Obtain and authenticate a REST API key from a third-party data provider.
2. Build a multi-step webhook automation (`Catch Hook` → `GET` → `Create Record`) to fetch and store external API data into a structured database table.
3. Configure an AI chatbot connected to a live knowledge table and trigger automated post-conversation logging.
4. Test, validate, and troubleshoot end-to-end API data retrieval and user prompt recording.

---

## System Architecture

```text
[External Trigger / Webhook URL]
        │
        ▼
┌──────────────────────────────────────────────────────────┐
│ Zap 1: Fetch API Data ([LastName]_API_Webhook_to_Table)  │
│  1. Webhooks by Zapier (Catch Hook)                      │
│  2. Webhooks by Zapier (GET -> REST API Endpoint)        │
│  3. Zapier Tables (Create Record -> Primary Data Table)  │
└──────────────────────────────────────────────────────────┘
        │
        ▼
[Zapier Table: Primary Knowledge Base]
        │  (Connected as Knowledge Source)
        ▼
┌──────────────────────────────────────────────────────────┐
│ AI Chatbot ([LastName]_AI_Search_Chatbot)                │
│  • Answers queries grounded in the connected Table       │
│  • Logic: Run Zap "At the end of the conversation"       │
└──────────────────────────────────────────────────────────┘
        │
        ▼
┌──────────────────────────────────────────────────────────┐
│ Zap 2: Log Prompts ([LastName]_Users_Prompt_Logger)      │
│  1. Zapier Chatbots (Chatbot Conversation Completed)     │
│  2. Zapier Tables (Create Record -> Users Prompt Table)  │
└──────────────────────────────────────────────────────────┘
```

---

## Prerequisites & Tools
* **Zapier Account** with access to:
  * **Zaps** & **Webhooks by Zapier**
  * **Zapier Tables**
  * **Zapier Chatbots**
* **Public REST API Account & Key:**
  * **Default Reference API:** RAWG Video Games Database API (`https://api.rawg.io/api/games`)
  * **Alternative:** Any instructor-approved public REST API (e.g., OpenWeather, TMDB, PokéAPI, NewsAPI)

---

## Step-by-Step Laboratory Instructions

### Part A: API Key Generation
1. Create or sign in to your chosen API provider account (e.g., `rawg.io`).
2. Open the **API / Developer Settings** section (`rawg.io/apidocs`).
3. Enter your application details (e.g., *"AI Search Application"*) and generate your **API Key**.
4. Copy and store your API key securely for **Part C**.

---

### Part B: Data Table Setup
In **Zapier Tables**, create two structured tables using the following schemas:

#### 1. Primary Data Table (`[LastName]_API_Data_Table`)
| Field Name | Data Type | Example Value |
| :--- | :--- | :--- |
| **Item / Title** | `Text` | Super Mario Bros. |
| **Date and Time** | `Date & time` | 11/25/2026 08:30 AM |
| **Category / Platform** | `Text` | Nintendo Entertainment System |

#### 2. User Prompt Log Table (`[LastName]_Users_Prompt`)
| Field Name | Data Type | Example Value |
| :--- | :--- | :--- |
| **User Prompt** | `Text` | `[Full Conversation Transcript]` |
| **Timestamp** | `Date & time` | 11/25/2026 08:42 AM |

---

### Part C: Zap 1 — Fetch API Data via Webhook
1. Create a new Zap named `[LastName]_API_Webhook_to_Table`.
2. **Trigger — `Webhooks by Zapier` (`Catch Hook`):**
   * Copy the generated **Webhook URL** (`https://hooks.zapier.com/hooks/catch/...`) and pull a sample test record.
3. **Action 1 — `Webhooks by Zapier` (`GET`):**
   * **URL:** `https://api.rawg.io/api/games` *(or your approved API endpoint)*
   * **Query String Params:**
     * `key`: `<YOUR_API_KEY>`
     * `search`: `mario` *(or your test search query)*
   * Run **Test step** to confirm valid JSON data is returned.
4. **Action 2 — `Zapier Tables` (`Create Record`):**
   * **Table ID:** `[LastName]_API_Data_Table`
   * Map the retrieved API response fields to your table columns *(avoid mapping unformatted JSON objects into `Date & time` fields)*.
   * Run **Test step**, verify the record appears in your table, and **Publish** the Zap.

---

### Part D: AI Chatbot Configuration
1. Navigate to **Zapier Chatbots** → **+ Create** and name your bot `[LastName]_AI_Search_Chatbot`.
2. Open **Instructions** and define the chatbot's persona, scope, and rule to ground responses in its connected knowledge table.
3. Open **Knowledge** → **+ Add source**:
   * **Type:** `Table`
   * **Table:** `[LastName]_API_Data_Table`

---

### Part E: Zap 2 — Automated Conversation Logging
1. Create a second Zap named `[LastName]_Users_Prompt_Logger`.
2. **Trigger — `Zapier Chatbots` (`Chatbot Conversation Completed`):**
   * Select `[LastName]_AI_Search_Chatbot` and click **Test trigger**.
3. **Action — `Zapier Tables` (`Create Record`):**
   * **Table ID:** `[LastName]_Users_Prompt`
   * **User Prompt:** Map to **Full Conversation Transcript**
   * **Timestamp:** Map to **Created At**
   * Run **Test step** and **Publish** the Zap.
4. Return to your chatbot's **Logic** panel, select **Run Zap**, set the condition to **At the end of the conversation**, and attach `[LastName]_Users_Prompt_Logger`.

---

### Part F: End-to-End Verification
1. Open your chatbot's preview or shareable link.
2. Conduct a live test conversation using at least **three (3) distinct prompts** related to your API dataset.
3. Confirm that the chatbot retrieves accurate answers from your knowledge table.
4. Allow the session to finish and verify that the conversation transcript and timestamp are automatically logged into `[LastName]_Users_Prompt`.

---

## Submission Guidelines
Submit your work by **creating a GitHub Issue** in this repository and attaching your **live chatbot link** along with your required lab details:
1. **Issue Title:** `[Task Sheet 8.1.1] LastName, FirstName - Chatbot Submission`
2. **Live Chatbot Link:** Paste your working, publicly accessible chatbot link directly in the issue body.
