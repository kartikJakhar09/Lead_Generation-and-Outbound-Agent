# Lead_Generation-and-Outbound-Agent

## Overview

Rioo Revenue Engine is an AI-powered outbound sales automation workflow built in **n8n**. It automates the complete outbound sales pipeline:

* Generate leads from LinkedIn (via Apify)
* *(Planned)* Generate leads from Apollo and Clay
* Normalize and deduplicate lead data
* Qualify leads using GPT
* Generate personalized cold emails
* Store leads in Airtable CRM
* Send outreach automatically through Gmail
* Schedule AI-generated follow-ups
* Track replies and update CRM stages
* Expose dashboard APIs for monitoring the sales pipeline

---

# Architecture

```
LinkedIn / Apollo / Clay
            │
            ▼
      Lead Collection
            │
            ▼
     Normalize Lead Data
            │
            ▼
     Deduplicate (Airtable)
            │
            ▼
      GPT Lead Qualification
            │
      ┌─────┴─────┐
      │           │
 High Score    Low Score
      │           │
      ▼           ▼
Generate Email   Store Lead
      │
      ▼
 Store in Airtable
      │
      ▼
 Send Gmail Outreach
      │
      ▼
 Update CRM Stage
      │
      ▼
 Automated Follow-up Engine
      │
      ▼
 Reply Detection & CRM Updates
```

---

# Requirements

Before importing the workflow, make sure the following accounts are available.

## 1. n8n

Self-hosted or Cloud version.

---

## 2. OpenAI API Key

Required for:

* Lead Qualification
* Outreach Email Generation
* Follow-up Email Generation

Steps

1. Create an OpenAI account.
2. Generate an API Key.
3. Create an **OpenAI Chat Model** credential in n8n.
4. Select the model (recommended: **gpt-4o-mini**).

Used by:

* AI Lead Qualifier
* Outreach Generator
* Follow-up Generator

---

## 3. Gmail OAuth

Required for:

* Sending cold emails
* Sending follow-up emails
* Monitoring inbox replies

Steps

1. Create Google OAuth credentials.
2. Configure OAuth Redirect URL.
3. Connect Gmail inside n8n.
4. Grant Gmail permissions.

Used by:

* Send Outreach Email
* Send Follow-up Email
* Gmail Trigger

---

## 4. Airtable

Required as the CRM.

Create:

* Base
* Leads table

The workflow stores:

* Name
* Company
* Email
* Profile URL
* Lead Score
* Priority
* Temperature
* Category
* AI Reasoning
* AI Email
* Stage
* Status
* Follow-up Count
* Last Contacted

After creating the base:

1. Generate a Personal Access Token.
2. Connect Airtable credentials in n8n.
3. Update:

   * Base ID
   * Table ID

---

## 5. Apify

Required for LinkedIn lead generation.

Steps

1. Create an Apify account.
2. Generate an API Token.
3. Replace the token in the HTTP Request node.
4. Configure the LinkedIn Actor.

Current workflow uses LinkedIn post search to discover prospects.

Future versions can replace or extend this with:

* Apollo
* Clay
* Sales Navigator
* Other lead providers

---

# Webhooks

The workflow exposes several endpoints.

## Lead Ingestion

```
POST /ingest-lead
```

Manually submit a lead.

Example:

```json
{
  "name": "",
  "title": "",
  "company": "",
  "email": "",
  "profile_url": "",
  "notes": ""
}
```

---

## Trigger Apify

```
POST /ingest-apify
```

Example:

```json
{
  "keyword": "AI Agency Founder",
  "max_results": 25
}
```

---

## Dashboard APIs

```
GET /dashboard-leads
```

Returns all leads.

```
GET /dashboard-stats
```

Returns pipeline statistics.

---

## Update Stage

```
POST /update-stage
```

Example

```json
{
    "record_id":"...",
    "stage":"Contacted",
    "status":"Emailed"
}
```

---

# Workflow Configuration

Update the following nodes after importing:

### Airtable

* Base ID
* Table ID
* Personal Access Token

---

### Gmail

Reconnect:

* Send Outreach Email
* Send Follow-up Email
* Gmail Trigger

---

### OpenAI

Reconnect:

* Lead Qualifier
* Outreach Generator
* Follow-up Generator

---

### Apify

Replace:

* API Token
* Actor ID (if using another actor)

---

# Scheduled Jobs

The workflow includes an automated follow-up scheduler.

Default:

* Runs every **12 hours**
* Finds leads due for follow-up
* Generates a new AI email
* Sends email
* Updates CRM

You can change the schedule inside the Schedule Trigger node.

---

# Dashboard

The workflow exposes APIs for any frontend dashboard.

Current endpoints:

* Pipeline Statistics
* All Leads
* Stage Updates

These endpoints can be connected to a React, Next.js, Streamlit, or Lovable dashboard.

---

# Future Improvements

* Apollo integration
* Clay integration
* Multi-provider lead enrichment
* Company website scraping
* Contact verification
* AI reply drafting
* Automatic meeting scheduling
* Slack notifications
* Multi-user CRM
* Vector memory for long-term prospect history

---

# Import Steps

1. Import the workflow JSON into n8n.
2. Reconnect all credentials.
3. Configure Airtable IDs.
4. Add your OpenAI API key.
5. Connect Gmail OAuth.
6. Add your Apify API token.
7. Activate the workflow.
8. Test `/ingest-lead` or `/ingest-apify`.
9. Verify that leads are stored in Airtable.
10. Confirm outreach emails are sent successfully.
11. Enable the Schedule Trigger for automated follow-ups.

---

# Tech Stack

* n8n
* OpenAI GPT-4o Mini
* Airtable
* Gmail
* Apify
* LinkedIn
* REST APIs

---

# Notes

* The workflow is designed to be modular—lead sources can be swapped without affecting downstream automation.
* Apollo and Clay integrations are planned and can be added by replacing the lead ingestion component while keeping the qualification, outreach, CRM, and follow-up pipelines unchanged.
* For production use, consider adding rate limiting, retry logic, secret management, logging, and monitoring to improve reliability and scalability.
