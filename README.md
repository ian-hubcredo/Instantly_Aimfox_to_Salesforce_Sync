# Instantly Salesforce Aimfox

## Overview

This workflow acts as a central webhook handler that syncs outreach activity from both Instantly (email) and Aimfox (LinkedIn) into Salesforce. When an email is opened, a reply is received, an email bounces, or a lead unsubscribes, the event is logged as a Salesforce task on the matching Contact or Lead record. If the person does not exist in Salesforce yet, a new Lead is created automatically. Bounces and unsubscribes also update the relevant record's description and opt-out status. This keeps your CRM timeline accurate across both email and LinkedIn channels without any manual data entry.

## How It Works

```
Instantly Webhook (POST) or Aimfox Webhook (POST) -> Normalize data -> Validate required fields -> Search Salesforce Contact + Lead by email -> Record exists? -> Route by event type (bounce / unsubscribe / other) -> Update Contact or Lead if needed -> Log Activity as Salesforce Task -> Return 200
```

If the record does not exist, a new Lead is created before logging the activity. If validation fails, an error task is logged and a 400 response is returned.

### Workflow Diagram

```mermaid
flowchart TD
    A["Instantly Webhook\n(POST)"] --> C["Normalize\nAll Data"]
    B["Aimfox Webhook\n(POST)"] --> C
    C --> D{"Validate\nRequired Fields"}
    D -- Valid --> E["Salesforce\nFind Contact by Email"]
    D -- Valid --> F["Salesforce\nFind Lead by Email"]
    D -- Invalid --> G["Log Error Task\nin Salesforce"]
    G --> H["Return 400"]
    E --> I{"Record\nExists?"}
    F --> I
    I -- Yes --> J{"Event Type?"}
    I -- No --> K["Salesforce\nCreate New Lead"]
    J -- Bounce --> L["Update Contact/Lead\nBounce Status"]
    J -- Unsubscribe --> M["Update Contact/Lead\nOpt-Out"]
    J -- Other --> N["Log Activity\nSalesforce Task"]
    L --> N
    M --> N
    K --> N
    N --> O["Return 200"]

    style A fill:#1B3A4B,color:#fff
    style B fill:#1B3A4B,color:#fff
    style C fill:#2C5F7C,color:#fff
    style E fill:#3D5A80,color:#fff
    style F fill:#3D5A80,color:#fff
    style K fill:#4A6D7C,color:#fff
    style N fill:#274C36,color:#fff
```

## Integrations

- **Instantly** - Email campaign event webhooks (opens, replies, bounces, unsubscribes)
- **Aimfox** - LinkedIn campaign event webhooks
- **Salesforce** - Contact/Lead lookup, creation, updates, and task logging

## Setup

1. Import `instantly_salesforce_aimfox.json` into your n8n instance.
2. Configure Salesforce OAuth2 credentials.
3. Register the webhook URLs in Instantly and Aimfox campaign settings.
4. Activate the workflow.
