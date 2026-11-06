# LinkedIn Automation & Analytics System

A collection of three n8n workflows built to streamline LinkedIn content creation, publishing approvals, failure notifications, and weekly performance reporting.

> **Portfolio/demo project:** These workflow exports are sanitized examples. They do not include credentials. Configure your own accounts and IDs in n8n before running them.

## Workflows

### 1. AI-Powered LinkedIn Content Automation
**File:** `workflows/linkedin_content_automation.json`

- Reads scheduled topics from a Google Sheets content calendar.
- Checks the scheduled date and pending status.
- Uses an AI agent (OpenAI Chat Model) to draft a beginner-friendly LinkedIn post.
- Creates an image brief and requests an image from Cloudflare Workers AI (FLUX).
- Emails the generated post for human approval.
- Waits for an approval/rejection decision.
- Checks whether a LinkedIn post ID already exists to avoid duplicate publishing.
- Publishes the approved post and updates the content calendar status and post metadata.

### 2. LinkedIn Automation Error Monitoring
**File:** `workflows/linkedin_error_monitoring.json`

- Starts from an n8n Error Trigger.
- Appends workflow failure details to a Google Sheets error log.
- Sends an email alert with workflow, execution, failed-node, and execution-link details.

### 3. LinkedIn Analytics & Weekly Reporting
**File:** `workflows/linkedin_analytics_reporting.json`

- Runs from a scheduled trigger (configured for 10:00; workflow timezone is Asia/Kolkata).
- Reads post metrics from Google Sheets.
- Calculates engagement rate as:

  `((reactions + comments + reposts) / impressions) * 100`

- Builds a rolling seven-calendar-day summary, including post count, impressions, reactions, comments, reposts, average engagement rate, and best-performing post.
- Emails the weekly summary.

**Important scope note:** The analytics workflow reads metrics already present in the spreadsheet. The exported workflow does not show a LinkedIn API step that fetches those metrics automatically.

## Tech stack

- n8n
- Google Sheets
- Gmail
- LinkedIn
- OpenAI Chat Model / AI Agent
- Cloudflare Workers AI (FLUX image generation)
- JavaScript (n8n Code nodes)

## High-level architecture

```text
Content calendar (Google Sheets)
            |
            v
Date/status filter --> AI post generation --> AI image brief --> Cloudflare image
            |                                             |
            +---------------------> Approval email <------+
                                      |
                                Human approval
                                      |
                             Duplicate-post check
                                      |
                             LinkedIn publishing
                                      |
                           Update calendar status

n8n Error Trigger --> Error log (Google Sheets) --> Gmail alert

Schedule Trigger --> Analytics sheet --> JavaScript calculations
                                      --> Weekly Gmail report
```

## Setup

1. Install or access an n8n instance.
2. Import the relevant JSON file from the `workflows/` folder.
3. Create your own Google Sheets, Gmail, LinkedIn, OpenAI, and Cloudflare credentials in n8n.
4. Re-select the correct credentials on the imported nodes.
5. Replace placeholder spreadsheet IDs, sheet/tab selections, recipient email, LinkedIn person identifier, and Cloudflare account configuration with your own values.
6. For Cloudflare, store the API token in a secure n8n credential or another supported secret-management method. Do **not** hardcode tokens in the HTTP Request node or commit them to Git.
7. Match the Google Sheets column names used by the workflows. Review the expressions and sheet tab names after import.
8. Test each workflow with sample data and confirm the approval, publishing, error alert, and reporting paths.
9. Activate scheduled/error-trigger workflows only after testing.

## Suggested spreadsheet tabs

The exports refer to a Google Sheets workbook with content calendar, error log, and analytics tabs. Use these fields as a starting point and adjust the node mappings to match your own sheet:

- **Content calendar:** topic ID, topic, scheduled date, status, generated post, approval status/date, LinkedIn post ID, published date, error message.
- **Error log:** timestamp, workflow name, execution ID/failed node, error details, execution URL.
- **Analytics:** topic ID, post date, post topic, impressions, reactions, comments, reposts, engagement rate.

## Security

- All credential objects have been removed from these sanitized exports.
- Personal email addresses and account-specific IDs have been replaced with placeholders.
- Add credentials only inside your own n8n instance.
- Never commit `.env` files, API tokens, OAuth tokens, execution payloads, or private spreadsheet data.
- If a real token was ever committed or shared, revoke/rotate it; deleting it from the latest file is not enough to remove it from Git history.

## Current status / limitations

The supplied exports had `active: false`, so the workflows are not active in the exported configuration. They need account credentials, valid spreadsheet mappings, and end-to-end testing before activation. The weekly analytics calculation depends on metrics being available in the analytics sheet.

## Repository structure

```text
linkedin-automation-analytics/
├── README.md
├── .gitignore
└── workflows/
    ├── linkedin_content_automation.json
    ├── linkedin_error_monitoring.json
    └── linkedin_analytics_reporting.json
```

## Learning outcomes

This project demonstrates practical experience with workflow orchestration, AI-assisted content generation, API integrations, human-in-the-loop approvals, conditional logic, duplicate prevention, error handling, JavaScript data processing, and scheduled reporting.
