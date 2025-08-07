# 📘 OpenSearch Alert to Znuny Ticket Integration

This guide explains how to send alerts from **OpenSearch** directly to **Znuny** via a custom **Webhook** integration. The integration allows OpenSearch alerts to automatically create tickets in Znuny with meaningful, dynamic content.

---

## 🛠️ Prerequisites

1. Working instance of **Znuny** with the GenericInterface enabled.
2. **OpenSearch** (with Alerting plugin and Notification channels enabled).
3. Znuny user with permission to create tickets (e.g., `alertagent@localhost`).

---

## 🔗 Znuny Endpoint Configuration

Enable Znuny's GenericInterface with a `TicketCreate` endpoint:

**POST endpoint:**

```
http://<znuny_host>/otrs/nph-genericinterface.pl/Webservice/<webservice_name>/TicketCreate
```

Enable HTTP Basic Auth or pass credentials as part of the JSON body.

---

## 📤 OpenSearch Webhook Configuration

Go to **Notifications > Channels** in OpenSearch and create a new **Webhook** channel.

Use the following payload template:

```json
{
  "UserLogin": "alertagent@localhost",
  "Password": "Passwd1!",
  "Ticket": {
    "Title": "Alert: {{ctx.monitor.inputs.0.queries.0.name}}",
    "Queue": "Raw",
    "State": "new",
    "Priority": "3 normal",
    "CustomerUser": "alertagent@localhost"
  },
  "Article": {
    "Subject": "SIEM Alert",
    "Body": "Query triggered: {{ctx.monitor.inputs.0.queries.0.query}}",
    "ContentType": "text/plain; charset=utf8"
  }
}
```

### 🔍 Notes on Mustache Variables:

- `{{ctx.monitor.inputs.0.queries.0.name}}` → Name of the triggered query.
- `{{ctx.monitor.inputs.0.queries.0.query}}` → The raw query string.
- `{{ctx.monitor.name}}` → Monitor name.
- `{{ctx.trigger.name}}` → Trigger name.

Make sure you're referencing the correct **array index**, especially if you have multiple inputs or queries.

---

## ✅ Expected Ticket Output in Znuny

- **Title**: Auto-filled with the query name (e.g., `Windows Whoami Execution`)
- **Body**: Contains the exact query used
- **Queue**: `Raw` (can be customized)

---

## 🧪 Troubleshooting

### 1. **Empty Fields in Znuny?**

✔ Check if you're accessing `.0` index in arrays like `inputs` or `queries`. ✔ Verify the output by logging `ctx` in OpenSearch alert preview.

### 2. **TicketNumber Format (e.g., **``**)?**

Znuny generates ticket numbers via system configuration. To customize:

- Go to **Znuny Admin > System Configuration**
- Search for: `Ticket::NumberGenerator` or `Ticket::NumberFormat`

---

## 📞 Support

For enhancements or bug reports, please contact your SIEM administrator or file an issue in your internal tracking system.

---

## 🧩 Bonus: Useful Mustache Variables

| Variable                                   | Description        |
| ------------------------------------------ | ------------------ |
| `{{ctx.monitor.name}}`                     | Monitor name       |
| `{{ctx.monitor.inputs.0.queries.0.name}}`  | Query name         |
| `{{ctx.monitor.inputs.0.queries.0.query}}` | Raw query string   |
| `{{ctx.trigger.name}}`                     | Trigger name       |
| `{{ctx.trigger.severity}}`                 | Severity level     |
| `{{ctx.alerts.0.execution_id}}`            | Alert execution ID |

---

Happy alerting! 🎉

