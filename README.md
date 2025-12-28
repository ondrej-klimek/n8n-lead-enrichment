# ⚡ Automated Lead Enrichment Pipeline

An automated research tool that takes a raw company domain (e.g., `hyundai.com`) and instantly populates a database with the company's full profile, including its logo, description, and social media links. Built with **n8n** and the **Brandfetch API**.

![Lead Enricher Dashboard](src/lead-enrich-dash.png)

## 🚀 The Problem
Sales and marketing teams spend countless hours manually researching leads. Copy-pasting company descriptions, hunting for transparent logos, and finding the right LinkedIn or Twitter profiles is tedious and prone to error.

## 💡 The Solution
I built a "Zero-Touch" enrichment pipeline. You simply paste a website domain into a Google Sheet, and the automation:
1.  **Detects** the new entry immediately.
2.  **Queries** an external API (Brandfetch) to gather intelligence.
3.  **Parses** the complex JSON response to find specific assets.
4.  **Updates** the row with a visual logo, bio, and social links.
5.  **Status Check:** Marks the row as "Complete" to prevent loops.

## 🛠️ Tech Stack
* **Orchestration:** [n8n](https://n8n.io/)
* **API:** [Brandfetch](https://brandfetch.com/developers) (HTTP Request Node)
* **Database:** Google Sheets
* **Key Techniques:** REST API (`GET`), JSON Array Search (`.find()`), Excel Formulas (`=IMAGE()`).

## ⚙️ Workflow Logic

### 1. The Watcher
* **Trigger:** Google Sheets (Row Added).
* **Filter:** Only processes rows where `Status` is equal to "Pending". This ensures the workflow is efficient and doesn't re-process completed leads.

### 2. The API Connector (The Core)
* **Node:** `HTTP Request`.
* **Method:** `GET` request to `api.brandfetch.io/v2/brands/{domain}`.
* **Security:** Uses Header Authentication (`Authorization: Bearer...`) to securely transmit the API key without exposing it in the URL.

### 3. Smart Data Parsing
The API returns a complex list of social links. Instead of hardcoding an index (e.g., "Link #4"), I used JavaScript to search the array dynamically:

```javascript
    // Robustly finds the Twitter URL, even if the order changes
    {{ $json.links.find(x => x.name === 'twitter')?.url }}
```

### 4. Visual Database Update
* **Node:** Google Sheets (Update Row).
* **Visual Feature:** Instead of pasting a raw image link, the workflow injects a spreadsheet formula:
    `=IMAGE("https://...")`
    This renders the company logo directly inside the cell for a visual dashboard experience.

## 🚀 How to Run This

1.  **Prerequisites:**
    * n8n (Cloud or Self-Hosted).
    * Free Brandfetch Developer Account (for the API Key).
    * Google Sheets account.
2.  **Google Sheet Setup:**
    * Create columns: `Domain`, `Company_Name`, `Description`, `Logo_URL`, `Twitter_Link`, `Status`.
    * *Tip:* Set the row height to **60px** to see the logos clearly.
3.  **n8n Setup:**
    * Import the `.json` file from this repo.
    * Set up a **Header Auth** credential for Brandfetch (`Authorization`: `Bearer [YOUR_KEY]`).
    * Connect your Google Drive credentials.
4.  **Usage:**
    * Add a domain (e.g., `tesla.com`) to the `Domain` column.
    * Set `Status` to `Pending`.
    * Watch the data fill in automatically!

## 📄 License

**© 2025 Ondrej Klimek. All Rights Reserved.**

This project is free to use for personal, educational, and portfolio review purposes.

**Commercial use, redistribution, or modification for business purposes is strictly prohibited without my written permission.**

If you would like to use this workflow in your business or hire me to build a similar solution, please contact me at: **klimekondrej01@gmail.com**
**IMPORTANT** - in the subject, include the following words and phrases: **permission**\*, **custom solution**\*\*, **Lead Enricher Pipeline**

\* *for commercial use permission*

\*\* *for building a custom solution for your business*
