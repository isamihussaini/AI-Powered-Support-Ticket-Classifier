# AI-Powered Support Ticket Classifier

An n8n workflow that reads incoming support emails directly from an inbox, classifies them with AI, drafts a reply, notifies the right team, and logs every ticket — with fallback handling if any step fails.

![Workflow Diagram](./AI-Powered_Support_Ticket_Classifier.png)

## The Problem

Every support inbox needs the same three things done for every email: someone has to read it and decide who it's for, someone has to write a reply, and someone has to make sure it doesn't get forgotten. Most small businesses don't have three people for this — they have one person doing all of it between everything else on their plate. That either slows down response times or eats hours every week that could go toward actual work.

## What This Workflow Does

1. **Email Trigger (IMAP)** — Reads new emails directly from a live inbox as they arrive, and marks them as read once processed so nothing gets picked up twice.

2. **Sanitize Email Body** — Long emails are trimmed to a safe length before being sent to the AI, keeping token usage predictable. The original message is never altered or lost — only what's sent to the AI is shortened.

3. **AI Classification (Groq)** — The email is classified into Billing, Tech, or Sales, given an urgency score from 1–5, and given a suggested reply. Automated notifications, newsletters, and non-actionable platform alerts (e.g. hosting or backup notices) are recognized and filtered out rather than treated as real tickets, so a team is never falsely alerted about something that isn't an actual customer inquiry.

4. **Manual Review Fallback** — If the AI can't confidently classify an email, it doesn't guess. The ticket is routed to a manual review queue instead of risking a wrong routing decision.

5. **Team Routing** — Based on the category, the right team is notified on Slack with full context: sender, subject, message, urgency, and the AI's suggested reply.

6. **Draft Reply** — A ready-to-review reply is created as a Gmail draft — never sent automatically. A human always reviews the full original email and the draft before anything goes to the customer.

7. **Verified Logging** — Every ticket is saved to Airtable. If the Airtable write fails for any reason, the ticket is automatically saved to a backup Google Sheet instead, and a failure notification is sent — so no ticket is ever silently lost.

## Why It's Built This Way

A workflow that runs without errors isn't the same as a workflow that behaves correctly. During testing, a state-handling issue was found where the workflow reused a previous test value instead of the newly received email — the execution looked successful, but the output was wrong. That's the exact kind of failure this project is built to catch: every external call (AI classification, CRM write) is treated as something to verify, not something to assume worked just because it didn't throw an error.

## Tools Used

`n8n` · `IMAP` · `Groq AI (Llama 3.1 8B)` · `Airtable` · `Gmail` · `Slack` · `Google Sheets` · `JavaScript` (custom parsing and sanitization logic)

## Files in This Repo

- `AI-Powered_Support_Ticket_Classifier.json` — Full exportable n8n workflow (import directly into your own n8n instance)
- `AI-Powered_Support_Ticket_Classifier.png` — Visual workflow diagram
- `README.md` — This file

## Setup

1. Import the JSON file into your n8n instance (`Import from File`).
2. Connect your own credentials for IMAP, Groq, Airtable, Gmail, and Slack.
3. Update the Slack channel IDs and Airtable base/table references to match your own setup.
4. In the "Email Trigger (IMAP)" node, confirm **Action** is set to **Mark As Read** so already-processed emails aren't picked up again.
5. Update the system prompt in the Groq node with your own business context so classification reflects your actual categories and use case.
6. Activate the workflow.

## About

Built by **Muhammad Sami Ullah** — AI Workflow Automation Specialist, specializing in n8n-based automation for small businesses and startups.
