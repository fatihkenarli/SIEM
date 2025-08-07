
# 🎯 OpenSearch → Znuny Webhook Integration (Ticket Creation)

This guide explains how to integrate **OpenSearch alerting** with **Znuny ticket system** using a **Webhook**. This setup will create a ticket in Znuny when an alert is triggered in OpenSearch.

---

## ✅ Step-by-Step Configuration

### 1. Create a Webhook Notification Channel in OpenSearch

- Go to **Alerting > Notification Channels**
- Click on **"Create channel"**
- Choose **Type**: `Webhook`
- Set:
  - **Method**: `POST`
  - **URL**: `http://<ZNUNY_HOST>/otrs/nph-genericinterface.pl/Webservice/GenericTicketConnectorREST/TicketCreate`
  - **Authentication**: None (or basic auth if needed)
  - **Headers**: Optional (e.g., Content-Type: application/json)

---

### 2. Configure Trigger → Notification

In the **Trigger configuration**, scroll to:

#### 🔹 Notification Message

- **Subject** (example):
  ```
  Triggered alert condition: {{ctx.trigger.name}} - Severity: {{ctx.trigger.severity}} - Threat detector: {{ctx.detector.name}}
  ```

- **Body** (exact working JSON example):

  ````json
  {
    "UserLogin": "alertagent@localhost",
    "Password": "Passwd1!",
    "Ticket": {
      "Title": "SIEM Test",
      "Queue": "Raw",
      "State": "new",
      "Priority": "3 normal",
      "CustomerUser": "alertagent@localhost"
    },
    "Article": {
      "Subject": "Alert",
      "Body": "Test from curl",
      "ContentType": "text/plain; charset=utf8"
    }
  }
  ````

📌 **Important:** Paste the JSON directly in the `Message Body` field without adding escape characters or formatting. Use double quotes (`"`) and correct commas. **No need to encode.**

---

## 🧠 Notes

1. `GenericTicketConnectorREST` webservice must be configured and active in Znuny.
2. The user used (`alertagent@localhost`) must have permission to create tickets.
3. You may customize fields dynamically using OpenSearch alert variables like `{{ctx.results.0.hits.0._source.message}}`.
4. A misconfigured body will cause Znuny to return `HTTP 500` or `400` errors.

---

## 🧪 Tested With

- OpenSearch 2.x Alerting Plugin
- Znuny 7.x with GenericInterface Webservice
- HTTP POST with JSON payload

---

## 🗂 Example Use Case

- SIEM detects suspicious PowerShell usage
- OpenSearch alert triggers this webhook
- Znuny ticket is created automatically with the details

---

## 🔐 Security Tip

Avoid hardcoding passwords in the alert body. Instead, use environment variables or vault-backed credentials if possible.
