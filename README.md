# 📅 

An **AI-powered n8n workflow** that automatically reviews your Google Calendar every morning, identifies the **top two most important meetings/events of the day**, explains why they are important, and sends a formatted summary to you via **Gmail**.

The workflow combines **n8n, Google Calendar, Gmail, Groq, and an AI Agent** to turn a busy calendar into a simple daily priority briefing.

---

## 🚀 What It Does

Every morning, the workflow:

1. ⏰ Runs automatically at **6:00 AM**
2. 📅 Fetches events from Google Calendar
3. 🤖 Uses an AI Agent to analyze the day's meetings
4. 🔍 Evaluates events using:

   * Event title
   * Event description
   * Attendees
5. ⭐ Selects the **two most important events**
6. 💡 Explains why each event is important
7. 📧 Sends the result through Gmail
8. 📝 Produces a nicely formatted HTML email

The workflow is configured to remain active and execute automatically.

---

## 🏗️ Workflow Architecture

```text
                 ┌─────────────────────┐
                 │   Schedule Trigger  │
                 │      6:00 AM        │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │      AI Agent       │
                 │                     │
                 │ Analyze Calendar    │
                 │ Events & Prioritize │
                 └───────┬─────┬───────┘
                         │     │
             ┌───────────┘     └────────────┐
             ▼                              ▼
   ┌─────────────────────┐       ┌─────────────────────┐
   │ Google Calendar Tool │       │    Groq Chat Model  │
   │                     │       │   GPT-OSS-120B      │
   │ Fetch Today's Events │       │                     │
   └─────────────────────┘       └─────────────────────┘
                         │
                         ▼
                 ┌─────────────────────┐
                 │     Gmail Tool      │
                 │                     │
                 │ Send Daily Summary  │
                 └─────────────────────┘
```

---

## 🧩 Technologies Used

| Technology          | Purpose                             |
| ------------------- | ----------------------------------- |
| **n8n**             | Workflow automation                 |
| **Google Calendar** | Source of calendar events           |
| **Gmail**           | Delivery of the daily briefing      |
| **AI Agent**        | Analyzes and prioritizes meetings   |
| **Groq**            | Provides the LLM                    |
| **GPT-OSS-120B**    | Language model used by the AI Agent |

The workflow uses the `openai/gpt-oss-120b` model through the Groq integration.

---

## ⚙️ Workflow Components

### 1. Schedule Trigger

The workflow starts automatically at **6:00 AM**.

```text
Schedule Trigger
       │
       ▼
    AI Agent
```

The n8n Schedule Trigger is configured with an hourly trigger value of `6`.

---

### 2. AI Agent

The AI Agent is responsible for deciding which meetings deserve the user's attention.

It analyzes:

* Meeting title
* Meeting description
* Meeting attendees
* Overall importance of the event

The goal is to identify the **top two most important events of the day** and explain the reasoning behind the selection.

---

### 3. Google Calendar

The Google Calendar tool retrieves calendar events using the `getAll` operation.

The configured calendar is:

```text
parag.saha911@gmail.com
```

The workflow sets the maximum time boundary dynamically using:

```javascript
$now.plus({ day: 1 })
```

This allows the workflow to retrieve upcoming events within the configured time window.

---

### 4. Groq AI Model

The AI Agent uses:

```text
openai/gpt-oss-120b
```

through the Groq Chat Model integration.

This model provides the reasoning capability required to evaluate the calendar events and determine their relative importance.

---

### 5. Gmail

The Gmail tool is connected to the AI Agent and allows the agent to dynamically generate:

* Recipient
* Subject
* Message

The workflow is designed to send a nicely formatted daily email containing the prioritized meetings and explanations.

---

## 🧠 AI Prioritization Logic

The assistant is instructed to determine importance based on the information available in each calendar event.

### Inputs

```text
Event Title
     +
Event Description
     +
Attendees
     ↓
AI Analysis
     ↓
Importance Ranking
     ↓
Top 2 Events
     ↓
Reasoning
```

For example, the AI could distinguish between:

```text
10:00 AM — Team Stand-up
11:30 AM — Client Strategy Review
02:00 PM — Internal Knowledge Sharing
04:00 PM — Leadership Review
```

and prioritize the events with greater business relevance based on their available context.

---

## 📧 Expected Email

A typical daily briefing could look like:

```text
Subject: 📅 Your Top Priorities for Today

Good morning!

Here are the two most important events on your calendar today:

━━━━━━━━━━━━━━━━━━━━━━
⭐ 1. Client Strategy Review
━━━━━━━━━━━━━━━━━━━━━━

Why it matters:
This meeting involves key stakeholders and focuses
on an important client/business discussion.

━━━━━━━━━━━━━━━━━━━━━━
⭐ 2. Leadership Review
━━━━━━━━━━━━━━━━━━━━━━

Why it matters:
The meeting includes senior stakeholders and may
require preparation or decision-making.

Have a productive day!
```

The actual recipient, subject, and message are generated through the AI Agent's Gmail tool parameters.

---

## 🔗 Node Connections

The workflow follows this structure:

```text
Schedule Trigger
       │
       ▼
   AI Agent
    ▲     ▲
    │     │
    │     └── Groq Chat Model
    │
    ├──────── Google Calendar
    │
    └──────── Gmail
```

The Schedule Trigger directly starts the AI Agent, while Google Calendar and Gmail are exposed to the AI Agent as tools. The Groq model is connected as the Agent's language model.

---

## 🔐 Required Credentials

To run this workflow, you need authenticated credentials for:

### Google Calendar

Required for:

* Reading calendar events
* Accessing event details

### Gmail

Required for:

* Sending the daily briefing email

### Groq

Required for:

* Accessing the configured LLM

The imported workflow already contains credential references, but when moving this workflow to another n8n instance, you should configure your own credentials.

> ⚠️ Never commit API keys, OAuth tokens, passwords, or credential exports to GitHub.

---

## 🛠️ Installation

### 1. Install n8n

Run n8n using your preferred setup.

For example:

```bash
npx n8n
```

or using Docker:

```bash
docker run -it --rm \
  -p 5678:5678 \
  n8nio/n8n
```

### 2. Import the Workflow

In n8n:

```text
Workflows
   ↓
Import from File
   ↓
Calendar Assistant.json
```

### 3. Configure Credentials

Connect:

```text
Google Calendar
Gmail
Groq
```

using your own credentials.

### 4. Verify Calendar

Make sure the Google Calendar configured in the workflow is the calendar you want the assistant to analyze.

### 5. Activate the Workflow

Once everything is configured:

```text
Workflow → Active
```

The workflow is already marked as active in the supplied JSON.

---

## ⚠️ Configuration Note

The AI Agent prompt contains **two different email addresses** in its instructions:

```text
parag.saha911@gmail.com
```

and

```text
aravindbharathykk@gmail.com
```

Meanwhile, the Google Calendar configured in the workflow is:

```text
parag.saha911@gmail.com
```

Before using this workflow in production, verify which email address should actually receive the daily briefing.

---

## 🔮 Possible Improvements

This workflow can be extended significantly.

### 1. Add Priority Scores

Instead of simply selecting two events, calculate:

```text
Importance Score =
Business Impact
+ Attendee Importance
+ Meeting Type
+ Urgency
+ Preparation Required
```

---

### 2. Add Meeting Preparation

For each important meeting, the AI could generate:

* Meeting objective
* People attending
* Topics to prepare
* Questions to ask
* Documents to review

---

### 3. Add Calendar Conflict Detection

The assistant could identify:

```text
⚠️ Scheduling Conflict

10:00 AM — Client Meeting
10:00 AM — Internal Review
```

and recommend which meeting should receive priority.

---

### 4. Add Daily Schedule Summary

Instead of only showing two meetings:

```text
Today's Schedule
────────────────────

09:00 — Stand-up
10:30 — Client Meeting ⭐
12:00 — Lunch
02:00 — Engineering Review
04:00 — Leadership Meeting ⭐
```

---

### 5. Add Slack / WhatsApp Notifications

The same AI-generated summary could be delivered through:

* Slack
* Microsoft Teams
* WhatsApp
* Telegram

---

### 6. Add Meeting Preparation Reminders

For example:

```text
⏰ 30 minutes before your most important meeting:

"Your client review starts in 30 minutes.

Attendees: 5
Priority: High

Preparation:
• Review pricing proposal
• Check latest metrics
• Prepare Q3 roadmap discussion"
```

---

## 📊 Benefits

### For Individuals

* Quickly understand the day's priorities
* Reduce calendar overload
* Avoid missing important meetings
* Start the day with context

### For Professionals

* Automatically prioritize client meetings
* Identify leadership interactions
* Prepare for high-impact discussions
* Reduce manual calendar review

### For Teams

The workflow can become a foundation for an AI-powered **personal productivity assistant** capable of combining calendar, email, tasks, and meeting intelligence.

---

## 🔒 Security Considerations

This workflow interacts with:

* Calendar data
* Meeting descriptions
* Attendee information
* Email

Therefore:

* Use OAuth credentials instead of hard-coded secrets.
* Never commit credentials to source control.
* Restrict Google/Gmail permissions to what the workflow actually needs.
* Review the information being sent to the external LLM.
* Avoid including confidential meeting information unless your organization's AI/data policies permit it.

---

## 📁 Project Structure

Recommended repository structure:

```text
calendar-assistant/
│
├── Calendar Assistant.json
├── README.md
└── .gitignore
```

Example `.gitignore`:

```gitignore
.env
*.key
credentials.json
.n8n/
node_modules/
```

---

## 🎯 Use Case

This project demonstrates how **AI Agents + workflow automation + business productivity tools** can be combined to create practical AI assistants.

Instead of manually checking a calendar every morning, the assistant proactively answers:

> **"What are the two things I should care about today, and why?"**

---

## 📌 Workflow Status

**Status:** Active
**Automation:** Daily
**Trigger:** 6:00 AM
**Calendar:** Google Calendar
**AI:** Groq / GPT-OSS-120B
**Output:** Gmail
**Primary Function:** Daily meeting prioritization

---

## 👨‍💻 Author

Built as an AI automation workflow using **n8n + Google Calendar + Gmail + Groq**.

---

## ⭐ Future Vision

The long-term goal can be to evolve this from a simple calendar summarizer into a **Personal AI Executive Assistant**:

```text
Calendar
   +
Email
   +
Tasks
   +
Contacts
   +
Meeting History
   +
AI
   ↓
Personal AI Assistant
   ↓
Priorities
Preparation
Reminders
Recommendations
Actions
```

The assistant would not only tell you **what matters today**, but also help you **prepare, decide, and act**.
