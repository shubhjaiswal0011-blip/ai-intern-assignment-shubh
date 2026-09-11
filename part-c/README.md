# AI Internship Assignment --- Shubh Jaiswal

A practical internship assignment demonstrating frontend development,
form handling, API integration, workflow automation, conditional lead
routing, email notification, Google Sheets integration, and scheduled
API workflows using n8n.

## Project Overview

This repository contains two primary parts:

-   **Part A --- Student Lead Capture Form**
-   **Part B --- n8n Workflow Automation**

The main automation flow is:

``` text
Student
   ↓
Lead Capture Form
   ↓
n8n Webhook
   ↓
Edit Fields
   ↓
IF Condition
   ├── PG / PhD → Gmail Notification
   └── UG       → Google Sheets
```

A separate scheduled workflow demonstrates retrieving weather data from
the Open-Meteo API.

## Repository Structure

``` text
ai-intern-assignment-shubh/
│
├── part-a/
│   └── index.html
│
├── part-b/
│   ├── Lead Notification Workflow (2).json
│   ├── Scheduled Weather Data Fetch.json
│   └── screenshots/
│
├── README.md
└── .gitignore
```

# Part A --- Student Lead Capture Form

## Overview

Part A implements a responsive **Student Lead Capture Form** using HTML,
CSS, and JavaScript.

The form collects:

-   Name
-   Email
-   Course Level
-   Country
-   University
-   Message

It also includes frontend interaction such as a message character
counter and form handling.

## Technologies

-   HTML5
-   CSS3
-   JavaScript
-   Web Forms
-   Webhook/API Integration

## Form Flow

``` text
Student
   ↓
Student Lead Capture Form
   ↓
Form Data
   ↓
n8n Webhook
```

# Part B --- n8n Workflow Automation

## Overview

Part B demonstrates workflow automation using **n8n**.

The workflow receives student lead information, extracts the required
fields, evaluates the course level, and routes the lead to the
appropriate action.

``` text
Webhook
   ↓
Edit Fields
   ↓
IF Condition
   ├── TRUE  → Gmail
   └── FALSE → Google Sheets
```

## 1. Webhook

The Webhook node is the entry point of the workflow and receives data
submitted by the lead capture form.

Example payload:

``` json
{
  "name": "Rahul Sharma",
  "email": "rahul.sharma@example.com",
  "courseLevel": "PG",
  "message": "I am interested in the program."
}
```

## 2. Edit Fields

The Edit Fields node extracts the required information and creates
structured fields:

``` text
name
email
courseLevel
message
```

Example expressions:

``` text
={{ $json.body.name }}
={{ $json.body.email }}
={{ $json.body.courseLevel }}
={{ $json.body.message }}
```

## 3. IF Condition

The IF node implements the assignment's routing logic.

``` text
PG  → TRUE
PhD → TRUE
UG  → FALSE
```

The conditions are:

``` text
courseLevel equals PG
OR
courseLevel equals PhD
```

### Routing Diagram

``` text
                 Course Level
                      ↓
                 ┌─────────┐
                 │ IF Node │
                 └────┬────┘
                      │
            ┌─────────┴─────────┐
            ↓                   ↓
          TRUE                FALSE
            ↓                   ↓
        PG / PhD               UG
            ↓                   ↓
          Gmail            Google Sheets
```

# Gmail Notification --- TRUE Branch

When the course level is **PG** or **PhD**, the IF node evaluates to
TRUE and sends the lead to Gmail.

``` text
PG / PhD Lead
     ↓
IF → TRUE
     ↓
Gmail
     ↓
Email Notification
```

Dynamic information can include:

``` text
Name: {{ $json.name }}
Email: {{ $json.email }}
Course Level: {{ $json.courseLevel }}
Message: {{ $json.message }}
```

# Google Sheets --- FALSE Branch

When the course level is **UG**, the IF node evaluates to FALSE and the
lead is stored in Google Sheets.

``` text
UG Lead
   ↓
IF → FALSE
   ↓
Google Sheets
   ↓
Append Row
```

The Google Sheets operation is:

``` text
Append Row
```

The spreadsheet contains:

  Column
  --------------
  Name
  Email
  Course Level
  Message

Example:

  --------------------------------------------------------------------------------
  Name              Email                      Course Level      Message
  ----------------- -------------------------- ----------------- -----------------
  Rahul Sharma      rahul.sharma@example.com   UG                Interested in the
                                                                 course

  Amit Kumar        amit@example.com           UG                Please share
                                                                 details
  --------------------------------------------------------------------------------

# Scheduled Weather Data Workflow

The repository also contains:

``` text
part-b/Scheduled Weather Data Fetch.json
```

This workflow demonstrates scheduled API integration using the
**Open-Meteo API**.

API endpoint:

``` text
https://api.open-meteo.com/v1/forecast
```

Example parameters:

``` text
Latitude: 28.6139
Longitude: 77.2090
Current: temperature_2m
```

These coordinates represent New Delhi, India.

## Weather Flow

``` text
Schedule Trigger
      ↓
Open-Meteo API
      ↓
Weather Data
      ↓
Processed Output
```

# Testing

## Test Case 1 --- UG

Input:

``` json
{
  "name": "Rahul Sharma",
  "email": "rahul.sharma@example.com",
  "courseLevel": "UG",
  "message": "Interested in the course."
}
```

Expected result:

``` text
IF → FALSE
     ↓
Google Sheets
     ↓
Append Row
```

## Test Case 2 --- PG

Input:

``` json
{
  "name": "Rahul Sharma",
  "email": "rahul.sharma@example.com",
  "courseLevel": "PG",
  "message": "Interested in the postgraduate program."
}
```

Expected result:

``` text
IF → TRUE
     ↓
Gmail
     ↓
Email Notification
```

## Test Case 3 --- PhD

Input:

``` json
{
  "name": "Rahul Sharma",
  "email": "rahul.sharma@example.com",
  "courseLevel": "PhD",
  "message": "Interested in the PhD program."
}
```

Expected result:

``` text
IF → TRUE
     ↓
Gmail
     ↓
Email Notification
```

# Setup Instructions

## 1. Clone the Repository

``` bash
git clone https://github.com/shubhJaiswal0011-blip/ai-intern-assignment-shubh.git
cd ai-intern-assignment-shubh
```

## 2. Run Part A

Open:

``` text
part-a/index.html
```

It can be opened directly in a browser or run using a local development
server such as VS Code Live Server.

## 3. Connect the Form to n8n

Configure the frontend with the appropriate n8n webhook URL.

Example:

``` javascript
const webhookURL = "YOUR_N8N_WEBHOOK_URL";
```

Do not expose private webhook URLs or credentials in the repository.

## 4. Configure n8n

### Webhook

Create/configure the webhook that receives the form submission.

### Edit Fields

Map:

``` text
name
email
courseLevel
message
```

using the webhook body expressions.

### IF Node

Configure:

``` text
courseLevel = PG
OR
courseLevel = PhD
```

Expected routing:

``` text
PG  → TRUE
PhD → TRUE
UG  → FALSE
```

### Gmail

Connect the TRUE output to Gmail and configure the recipient, subject,
and message.

### Google Sheets

Connect the FALSE output to Google Sheets and use:

``` text
Operation: Append Row
```

Map:

``` text
Name
Email
Course Level
Message
```

# Security

Never commit sensitive credentials.

Do not commit:

``` text
API keys
OAuth tokens
Gmail credentials
Google credentials
n8n credentials
GitHub Personal Access Tokens
Passwords
.env files containing secrets
Private secrets
```

Recommended `.gitignore`:

``` gitignore
.DS_Store
.env
node_modules/
```

# Technologies Used

  Technology       Purpose
  ---------------- ------------------------------------
  HTML5            Form structure
  CSS3             Frontend styling
  JavaScript       Form interaction and data handling
  n8n              Workflow automation
  Webhooks         Receiving form submissions
  Gmail            Email notifications
  Google Sheets    Lead storage
  Open-Meteo API   Weather data
  Git              Version control
  GitHub           Source code hosting

# Key Features

-   Responsive student lead capture form
-   Structured lead data collection
-   Frontend form handling
-   n8n webhook integration
-   Data transformation using Edit Fields
-   Conditional routing using IF logic
-   PG lead email notification
-   PhD lead email notification
-   UG lead storage in Google Sheets
-   Google Sheets Append Row operation
-   Scheduled weather API workflow
-   REST API integration
-   JSON-based n8n workflow exports
-   Git/GitHub version control

# Learning Outcomes

This assignment demonstrates practical knowledge of:

### Frontend Development

-   HTML form creation
-   CSS styling
-   JavaScript
-   Client-side interaction
-   Form data handling

### API Integration

-   Webhooks
-   HTTP requests
-   REST API concepts
-   JSON payloads
-   External API integration

### Workflow Automation

-   n8n workflow design
-   Webhook triggers
-   Data transformation
-   Conditional logic
-   Gmail integration
-   Google Sheets integration
-   Scheduled workflows

### Version Control

-   Git initialization
-   Git staging
-   Git commits
-   Git branches
-   GitHub remote repositories
-   Git push
-   Repository documentation

# Complete System Architecture

``` text
                         ┌─────────────────┐
                         │     Student     │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │  Lead Capture   │
                         │      Form       │
                         │   HTML/CSS/JS   │
                         └────────┬────────┘
                                  │
                                  │ Form Data
                                  ▼
                         ┌─────────────────┐
                         │   n8n Webhook   │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │   Edit Fields   │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │   IF Condition  │
                         │ PG/PhD → TRUE   │
                         │ UG → FALSE      │
                         └───────┬─┬───────┘
                                 │ │
                         TRUE ───┘ └──── FALSE
                           │                │
                           ▼                ▼
                    ┌─────────────┐  ┌──────────────┐
                    │    Gmail    │  │ Google Sheets│
                    └─────────────┘  └──────────────┘
```

# Assignment Summary

  Part               Implementation               Technology
  ------------------ ---------------------------- -----------------------
  Part A             Student Lead Capture Form    HTML, CSS, JavaScript
  Part B             Lead Notification Workflow   n8n
  PG Routing         Email notification           n8n + Gmail
  PhD Routing        Email notification           n8n + Gmail
  UG Routing         Spreadsheet storage          n8n + Google Sheets
  Weather Workflow   Scheduled API fetch          n8n + Open-Meteo
  Version Control    Source code management       Git + GitHub

# Future Improvements

Possible extensions include:

-   Stronger frontend validation
-   Form submission success/error notifications
-   Duplicate lead detection
-   Database storage
-   Lead status tracking
-   Automated follow-up emails
-   WhatsApp notifications
-   CRM integration
-   Lead analytics dashboard
-   More detailed weather processing
-   Error handling and retry mechanisms
-   Production deployment
-   Environment-based configuration
-   Centralized logging and monitoring

# Important Files

### `part-a/index.html`

Student Lead Capture Form and frontend logic.

### `part-b/Lead Notification Workflow (2).json`

Exported n8n lead notification workflow.

``` text
Webhook
   ↓
Edit Fields
   ↓
IF
   ├── TRUE → Gmail
   └── FALSE → Google Sheets
```

### `part-b/Scheduled Weather Data Fetch.json`

Scheduled Open-Meteo weather API workflow.

# Author

## Shubh Jaiswal

**B.Tech --- Computer Science & Engineering**

Areas of interest:

-   Artificial Intelligence
-   Machine Learning
-   Generative AI
-   AI Agents
-   Workflow Automation
-   API Integration
-   Software Development

GitHub: **shubhJaiswal0011-blip**

# Final Workflow Logic

``` text
Course Level
     │
     ├── PG ────→ Gmail Notification
     │
     ├── PhD ───→ Gmail Notification
     │
     └── UG ────→ Google Sheets
```

The project demonstrates the complete automation pipeline:

**Frontend → Webhook → Data Processing → Conditional Routing → Automated
Action**

along with a separate scheduled API workflow for weather data retrieval.

# License

This project was created as part of an internship assignment and is
intended for educational, demonstration, and evaluation purposes.
