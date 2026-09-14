# teleOptimise
# AI-PBX Platform

> **AI-first, self-hosted, multi-tenant business communication platform.**

---

## 🚀 What Are We Building?

We are building a modern alternative to traditional business phone systems such as **3CX**, using open/self-hostable technologies wherever practical.

The platform combines:

```text
FreePBX / Asterisk
        +
SIP / WebRTC
        +
Flutter Softphone
        +
Angular Admin Portal
        +
Laravel Control Plane
        +
Python AI Services
        +
AI Receptionists
        +
Visual IVR
        +
Automations
        +
Call Recording
        +
AI Analytics
```

### Long-Term Vision

Turn a traditional:

> **PBX / Phone System**

into:

> **AI-powered Business Communication Operating System**

---

# 🧠 IMPORTANT — READ THIS FIRST

This project is designed for **AI-assisted / vibe-coded development**.

Before changing code, an AI coding agent MUST understand the project architecture and development rules.

### Required reading order

```text
00-README.md
      ↓
01-PROJECT-VISION.md
      ↓
02-ARCHITECTURE.md
      ↓
03-PREREQUISITES.md
      ↓
04-SPRINT-ROADMAP.md
      ↓
05-VIBE-CODING-RULES.md
```

Do NOT skip the architecture and coding rules.

---

# 🎯 Current Goal

The immediate goal is to build a reliable MVP.

```text
DID
 ↓
FreePBX / Asterisk
 ↓
IVR / Call Routing
 ↓
AI Receptionist
 ↓
Human Transfer
 ↓
Flutter Softphone
 ↓
Recording + Transcript
 ↓
Laravel
 ↓
Angular Admin
```

The system must support:

> **Multiple companies using one platform while keeping their data completely isolated.**

---

# 🏗️ High-Level Architecture

```text
                         AI-PBX PLATFORM
                                │
        ┌───────────────────────┼───────────────────────┐
        │                       │                       │
        ▼                       ▼                       ▼
   USER PLANE             CONTROL PLANE             AI PLANE
        │                       │                       │
     Flutter                 Laravel                 Python
    Softphone                  API                 AI Services
        │                       │                       │
        └───────────────────────┼───────────────────────┘
                                │
                                ▼
                       TELEPHONY PLANE
                                │
                       FreePBX / Asterisk
                                │
                          SIP / WebRTC
                                │
                                ▼
                           DID / Trunk
```

---

# 🧩 Main Components

| Component      | Technology         | Responsibility                   |
| -------------- | ------------------ | -------------------------------- |
| Telephony      | FreePBX + Asterisk | SIP, calls, IVR, queues, routing |
| Mobile         | Flutter            | Employee softphone               |
| Admin          | Angular            | Business administration          |
| Backend        | Laravel            | Control plane / APIs             |
| AI             | Python             | AI agents and orchestration      |
| Database       | PostgreSQL         | Application data                 |
| Cache          | Redis              | Cache, queues, events            |
| Infrastructure | Docker             | Local/deployment environment     |

---

# 📱 Flutter

Flutter is the employee/mobile communication application.

Initial features:

```text
Login
SIP Registration
Dial Pad
Incoming Call
Outgoing Call
Active Call
Mute
Speaker
Hold
Transfer
Conference
Call History
Contacts
Presence
Voicemail
Recordings
```

Flutter is NOT the source of truth for business configuration.

Business configuration belongs to Laravel.

---

# 🖥️ Angular Admin Portal

Angular is the administration interface.

Expected modules:

```text
Dashboard
Companies
Users
Roles
Phone Numbers
Extensions
Departments
IVR
Call Flows
AI Agents
Knowledge Base
Automations
Calls
Recordings
Transcripts
Analytics
Settings
```

Angular communicates with Laravel.

Angular must NOT directly manipulate the PBX.

---

# 🔧 Laravel

Laravel is the **Control Plane**.

It manages:

```text
Authentication
Authorization
Companies
Users
Roles
Tenants
Extensions
Phone Numbers
Departments
AI Agents
Knowledge
Automations
Call Metadata
Recordings Metadata
API Access
Configuration
```

Laravel communicates with:

```text
Angular
Flutter
FreePBX / Asterisk
Python AI
PostgreSQL
Redis
```

---

# 🤖 Python AI

Python is the **AI Plane**.

Responsibilities:

```text
AI Receptionist
AI Agents
Speech Processing
AI Orchestration
Knowledge Retrieval
Tool Calling
Conversation Context
Call Summaries
Transcription
AI Analytics
Future AI Video
```

AI agents must NOT have unrestricted access to the database.

They interact through controlled tools/APIs.

---

# ☎️ FreePBX / Asterisk

FreePBX/Asterisk is the **Telephony Engine**.

It handles:

```text
SIP
PJSIP
DID
Extensions
IVR
Queues
Ring Groups
Call Routing
Voicemail
Conference
Call Recording
RTP
WebRTC
```

Do not rebuild PBX functionality unnecessarily.

Use Asterisk for telecommunications functionality.

---

# 🏢 Multi-Tenancy

The platform must be multi-tenant from the beginning.

Example:

```text
Platform
│
├── Company A
│   ├── Users
│   ├── Extensions
│   ├── Phone Numbers
│   ├── IVR
│   ├── AI Agents
│   └── Recordings
│
├── Company B
│   ├── Users
│   ├── Extensions
│   ├── Phone Numbers
│   ├── IVR
│   ├── AI Agents
│   └── Recordings
│
└── Company C
```

Most tenant-owned database records should contain:

```text
tenant_id
```

Tenant isolation is a security requirement, not an optional feature.

---

# 🤖 AI Receptionist

The first major AI feature is an AI receptionist.

Example:

```text
Customer
   ↓
DID
   ↓
Asterisk
   ↓
AI Receptionist
   ↓
Understand Intent
   │
   ├── Answer
   ├── Transfer
   ├── Book Appointment
   ├── Take Message
   ├── Create Lead
   └── Trigger Automation
```

The AI receptionist should behave naturally and professionally.

---

# 🧑‍💼 AI Agent Matrix

The platform should eventually support specialized AI agents.

```text
AI Agents
│
├── Receptionist
├── Sales
├── Support
├── Appointment
├── Follow-up
├── Collection
└── Future Custom Agents
```

Every agent should have:

```text
Identity
Purpose
Instructions
Personality
Knowledge
Tools
Permissions
Voice
Language
Tenant
Department
```

---

# 🔀 Visual IVR

The goal is to hide Asterisk complexity from normal users.

Eventually:

```text
Incoming Call
      ↓
Business Hours
      ↓
AI / IVR
   ┌──┼────┐
   ↓  ↓    ↓
 Sales Support Billing
```

Users should configure call flows visually.

Advanced users may access advanced configuration later.

---

# ⚡ Automation Engine

Automation follows:

```text
TRIGGER
   ↓
CONDITION
   ↓
ACTION
```

Example:

```text
Missed Call
    ↓
New Customer?
    ↓
Create Lead
    ↓
Notify Sales
    ↓
Send SMS
```

Possible triggers:

```text
Incoming Call
Missed Call
Completed Call
AI Transfer
Appointment Booked
New Lead
Voicemail
Customer Created
```

---

# 🎥 Future AI Video

AI video is a future phase.

The same AI agent should eventually support:

```text
             AI AGENT
                 │
       ┌─────────┼─────────┐
       ↓         ↓         ↓
     Voice      Chat      Video
       │         │         │
      SIP        Web     WebRTC
```

AI video must NOT delay the MVP.

---

# 🎙️ Recording

Call recording architecture:

```text
Call
 ↓
Recording
 ↓
Storage
 ↓
Transcript
 ↓
AI Summary
 ↓
Analytics
```

Recording access must respect:

```text
Tenant
Role
Permissions
Retention Policy
```

---

# 📊 AI Analytics

Future analytics may include:

```text
Call Volume
Call Duration
Missed Calls
Transfer Rate
Resolution Rate
Lead Quality
Customer Sentiment
AI Performance
Human Agent Performance
```

---

# 🗂️ Project Structure

Target structure:

```text
ai-pbx/
│
├── backend/
│   └── laravel/
│
├── admin/
│   └── angular/
│
├── mobile/
│   └── flutter/
│
├── ai/
│   └── python/
│
├── infrastructure/
│   ├── docker/
│   ├── freepbx/
│   ├── asterisk/
│   ├── postgres/
│   └── redis/
│
├── docs/
│
├── scripts/
│
├── tests/
│
└── README.md
```

---

# 📚 Documentation

Project documentation is located under:

```text
docs/
```

Core documents:

```text
00-README.md
01-PROJECT-VISION.md
02-ARCHITECTURE.md
03-PREREQUISITES.md
04-SPRINT-ROADMAP.md
05-VIBE-CODING-RULES.md
```

Additional technical documentation will be added as the project grows.

Expected future documents:

```text
06-DATABASE.md
07-API.md
08-FREEPBX.md
09-ASTERISK.md
10-SIP.md
11-FLUTTER-SOFTPHONE.md
12-LARAVEL.md
13-ANGULAR.md
14-AI-AGENTS.md
15-IVR.md
16-AUTOMATIONS.md
17-RECORDING.md
18-MULTI-TENANCY.md
19-SECURITY.md
20-SCALABILITY.md
21-TESTING.md
22-DEPLOYMENT.md
23-MONITORING.md
24-AI-VIDEO.md
```

---

# 🧠 AI Coding Agent Instructions

Every AI coding agent must follow this process:

```text
READ
 ↓
UNDERSTAND
 ↓
PLAN
 ↓
IMPLEMENT
 ↓
TEST
 ↓
DOCUMENT
 ↓
REVIEW
 ↓
COMMIT
```

Before coding:

1. Read `01-PROJECT-VISION.md`.
2. Read `02-ARCHITECTURE.md`.
3. Identify the affected module.
4. Read its technical documentation.
5. Check existing code before creating new code.
6. Follow `05-VIBE-CODING-RULES.md`.

---

# 🚫 Do Not Do This

Never:

```text
❌ Rewrite the architecture without approval
❌ Create duplicate services
❌ Put business logic inside Flutter UI
❌ Put business logic inside Angular components
❌ Allow Angular to directly control Asterisk
❌ Allow AI agents unrestricted database access
❌ Bypass Laravel APIs
❌ Hard-code tenant IDs
❌ Hard-code credentials
❌ Commit API keys
❌ Commit SIP passwords
❌ Ignore tenant isolation
❌ Add unnecessary dependencies
❌ Introduce microservices without a reason
❌ Introduce Kubernetes prematurely
❌ Build AI video before the MVP
```

---

# 🎯 Development Strategy

Build vertically in small working increments.

Bad:

```text
Build entire PBX
+
Build entire Flutter app
+
Build entire AI system
+
Build entire Admin
```

Good:

```text
Extension
 ↓
SIP Call
 ↓
Flutter Call
 ↓
Recording
 ↓
Laravel Record
 ↓
Angular Display
```

Every sprint should produce something testable.

---

# 🏁 MVP

The MVP should contain:

```text
✓ FreePBX / Asterisk
✓ SIP
✓ DID integration
✓ Extensions
✓ Flutter Softphone
✓ Basic IVR
✓ Call Routing
✓ Call Recording
✓ Call History
✓ Laravel Backend
✓ Angular Admin
✓ Multi-Tenancy
✓ AI Receptionist
✓ AI FAQ
✓ AI Transfer
✓ AI Call Summary
```

---

# ⏸️ Deferred Features

Do NOT allow these features to delay MVP:

```text
AI Video
Advanced CRM
Billing
Marketplace
WhatsApp ecosystem
Advanced Fine-Tuning
Kubernetes
Multi-Region Deployment
Advanced AI Analytics
Large AI Agent Marketplace
```

---

# 💰 Cost Philosophy

The project aims for **free/open-source software wherever practical**.

However:

> The platform itself can be self-hosted, but real-world communication and AI services may still have costs.

Potential external costs:

```text
DID
SIP Trunk
Server
Bandwidth
Storage
SMS
WhatsApp
AI API
Email
```

The architecture should keep external providers replaceable.

---

# ⚖️ External Code / License Rule

External repositories may be used as:

```text
Reference
Prototype
Dependency
Integration Source
```

Before copying or modifying external code:

```text
1. Check license
2. Check dependencies
3. Check security
4. Check maintenance
5. Check compatibility
6. Document reuse
7. Preserve license requirements
```

The current AI Receptionist reference project uses an **AGPL-3.0 license**.

Do not blindly copy its code into this project without reviewing the licensing implications.

---

# 🧪 Definition of Done

A feature is complete only when:

```text
✓ Implementation complete
✓ Error handling complete
✓ Security considered
✓ Tenant isolation verified
✓ Tests added where appropriate
✓ Manual testing completed
✓ API documented
✓ UI documented where required
✓ Technical documentation updated
✓ No unnecessary code duplication
✓ Git commit created
```

"Works on my machine" is NOT the definition of done.

---

# 🧩 Sprint Philosophy

Every sprint must have:

```text
GOAL
 ↓
TASKS
 ↓
SUBTASKS
 ↓
IMPLEMENTATION
 ↓
TEST
 ↓
DOCUMENTATION
 ↓
DEFINITION OF DONE
```

Tasks should be small enough that one person can clearly understand:

> What am I doing right now?

---

# 🧠 ADHD-Friendly Rule

The project should always expose **one immediate priority**.

Avoid presenting 50 tasks as if they all need to be completed simultaneously.

Use:

```text
CURRENT PRIORITY
      ↓
NEXT TASK
      ↓
SUBTASKS
      ↓
DONE
```

Then move to the next task.

---

# 🚦 Current Development Priority

## NOW

```text
Architecture
 ↓
FreePBX / Asterisk
 ↓
SIP
 ↓
Flutter Softphone
```

## NEXT

```text
Laravel
 ↓
Angular
 ↓
Multi-Tenant
```

## THEN

```text
IVR
 ↓
Recording
 ↓
AI Receptionist
```

## AFTER MVP

```text
Automation
 ↓
Agent Matrix
 ↓
AI Analytics
 ↓
AI Video
 ↓
Scaling
```

---

# 📌 First Milestone

The first technical milestone is extremely simple:

> **Two SIP endpoints can reliably call each other through Asterisk.**

Then:

> **Flutter can register as a SIP endpoint and make/receive calls.**

Then:

> **A real DID can enter the system and route to a configured destination.**

Then:

> **AI can answer the call and transfer it to a human.**

These milestones build on each other.

---

# 🏆 Ultimate Success Scenario

The platform reaches its first major production milestone when:

```text
Customer
   │
   │ Calls Company DID
   ▼
FreePBX / Asterisk
   │
   ▼
AI Receptionist
   │
   ├── Answers Question
   │
   ├── Books Appointment
   │
   ├── Creates Lead
   │
   └── Transfers Call
            │
            ▼
      Flutter Softphone
            │
            ▼
        Human Agent
            │
            ▼
      Recording + Transcript
            │
            ▼
        AI Summary
            │
            ▼
      Laravel Database
            │
            ▼
      Angular Dashboard
```

All of this must work for:

```text
Company A
Company B
Company C
...
Company N
```

without tenant data leakage.

---

# 🔥 Golden Rule

> **Build a reliable communication platform first. Then make it intelligent. Then make it autonomous.**

```text
Reliable PBX
     ↓
Modern Softphone
     ↓
Easy Administration
     ↓
Multi-Tenancy
     ↓
AI Receptionist
     ↓
Automation
     ↓
AI Employees
     ↓
AI Video
     ↓
Business Operating System
```

---

# 📖 Start Here

If you are a developer:

```text
Read:
01-PROJECT-VISION.md
02-ARCHITECTURE.md
03-PREREQUISITES.md
04-SPRINT-ROADMAP.md
05-VIBE-CODING-RULES.md
```

If you are an AI coding agent:

```text
STOP.

Do not modify code yet.

Read the project documentation first.
Determine the current sprint.
Determine the current task.
Inspect existing implementation.
Then propose the smallest safe change.
```

---

# 🚀 Project Status

**Current Phase:** Architecture / Foundation

**Current Priority:**

```text
Architecture
→ PBX
→ SIP
→ Flutter Softphone
```

**MVP Status:** Not started

**Production Status:** Not ready

**AI Video:** Future

**Primary Goal:** Build a scalable, easy-to-configure, AI-first, multi-tenant communication platform.

---

# End

**AI-PBX Platform**

> Simple for businesses.
> Powerful for developers.
> Intelligent by design.
