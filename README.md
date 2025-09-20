# Civic Complaint Processing Workflow – n8n Automation

This repository contains an **n8n workflow** that automates the intake, classification, summarization, geocoding, and email notification of civic complaints submitted via a webhook. The workflow leverages **OpenAI GPT models** for natural language understanding and **OpenStreetMap API** for location data.


<img width="1024" height="1024" alt="Gemini_Generated_Image_bxojj4bxojj4bxoj" src="https://github.com/user-attachments/assets/705dd761-bed1-4f3a-8b94-2df544e25765" />

---

## **Workflow Overview**

### 1. Webhook

* **Node:** `Webhook`
* **Purpose:** Receives POST requests containing civic complaint data.
* **Endpoint ID:** `aa85fa84-25b8-46d8-9ddf-2ead796187ca`

---

### 2. Input Validation

* **Node:** `Input Validation1`
* **Purpose:** Ensures the incoming request contains required fields (e.g., complaint text).
* **If empty:** Responds with `Validation Error1` JSON: `{"success": false, "error": "Invalid input"}`

---

### 3. Generate Complaint ID

* **Node:** `Generate ID1`
* **Purpose:** Generates a unique complaint ID using the timestamp format: `CMP_<timestamp>`

---

### 4. Image Analysis

* **Node:** `image analyis\`
* **Purpose:** Sends complaint image to OpenAI for analysis. Returns textual content describing the image.

---

### 5. Complaint Classification & Summarization

* **Node:** `complaint analysis`
* **Purpose:**

  * Classifies complaint into categories: `Garbage`, `Potholes`, `Street Lights`, `Trees`, `Other civic issue`.
  * Extracts key details in simple Hindi: what, where, urgency.
  * Outputs structured JSON:

```json
{
  "category": "...",
  "summary": "...",
  "location": "...",
  "user_name": "...",
  "phone": "..."
}
```

* **Node:** `summary maker`

  * Converts structured complaint data and location info into **HTML snippet** for visualization.

---

### 6. Geocoding

* **Node:** `Geocoding1`
* **Purpose:** Converts latitude and longitude to a full address using **OpenStreetMap Nominatim API**

---

### 7. Data Merge & Aggregation

* **Nodes:** `Merge` → `Aggregate`
* **Purpose:** Combines AI-generated complaint details and geocoding information for final output.

---

### 8. HTML Generation

* **Node:** `HTML`
* **Purpose:** Converts structured data into **a clean, styled HTML snippet** for email delivery.

---

### 9. Email Notification

* **Node:** `Send a message`
* **Purpose:** Sends the HTML-formatted complaint summary to the concerned authority via Gmail.
* **Recipient:** `anurag.srivastav@swaransoft.com`
* **Subject:** `Complaint`

---
### 10. Success Response

* **Node:** `Success Response`
* **Purpose:** Sends JSON response back to the user confirming receipt:

```json
{
  "success": true,
  "complaint_id": "<generated_id>"
}
```

---

## **Workflow Connections**

* Webhook triggers multiple nodes in parallel:

  * Image analysis
  * Input validation

* AI nodes (`OpenAI Chat Model2`) feed classification and summary data back into the workflow.

* Aggregated data flows through HTML generation and finally triggers email notification.

---

## **Technologies & Tools**

* **n8n** – Workflow automation platform
* **OpenAI GPT Models** – Complaint analysis, summarization
* **OpenStreetMap Nominatim API** – Geocoding
* **Gmail Node** – Sending notification emails
---

## **Usage**

1. Deploy this workflow in **n8n cloud or local instance**.
2. Configure webhook URL in your application.
3. Ensure **OpenAI API key** and **Gmail credentials** are set in n8n credentials.
4. POST complaint JSON with fields like `body` and `image`.
5. The workflow processes the complaint, classifies it, generates an HTML summary, sends an email, and returns a JSON response.

---

## **Sample Input**

```json
{
  "body": {
    "text": "सड़क पर गड्ढा हुआ है",
    "latitude": 28.410186,
    "longitude": 77.0510506,
    "image": "data:image/png;base64,<base64string>"
  }
}
```

---

## **Sample Output**

```json
{
  "success": true,
  "complaint_id": "CMP_1695212800000"
}
```

* Email sent includes formatted HTML with **category, summary, location, user info, and complaint ID**.

---

## **Notes**

* Ensure OpenAI API key has access to **gpt-4.1-mini** or equivalent.
* Gmail node requires **“Allow less secure apps”** or OAuth setup.
* Workflow handles **missing fields** gracefully by populating `"Not Provided"` in the HTML display.

---

This workflow provides a **fully automated, end-to-end civic complaint processing system** ready for deployment in urban governance projects.
