# AI-PBX Platform — Project Vision

**Document:** `01-PROJECT-VISION.md`
**Version:** 1.0
**Status:** Master Vision Document
**Purpose:** Defines what the platform is, why it exists, what we are building, and what we are NOT building.

---

# 1. Executive Vision

We are building a **self-hosted, multi-tenant AI-PBX and business communication platform** designed to compete with traditional systems such as 3CX while providing a significantly easier configuration experience and deeper AI capabilities.

The platform combines:

* FreePBX / Asterisk
* SIP / VoIP
* Flutter Softphone
* Angular Admin Portal
* Laravel Middleware / Control Plane
* Python AI Services
* AI Voice Receptionists
* Visual IVR
* Visual Automation
* Call Recording
* Call Transcription
* AI Analytics
* Multi-company / Multi-tenant architecture
* AI Agent Management
* Future AI Video Receptionists

The core philosophy is:

> **Complex telecommunications infrastructure should feel simple to the business owner.**

A business owner should not need to understand:

* SIP configuration
* PJSIP
* Asterisk dial plans
* RTP
* SDP
* SIP trunks
* IVR configuration
* Linux services
* Docker
* API integrations
* AI model configuration

The platform should hide this complexity behind a clean and easy-to-understand interface.

---

# 2. Product Vision

The long-term goal is to create:

> **An AI-first alternative to traditional business phone systems.**

Instead of only providing:

```text
Phone System
+
Extensions
+
IVR
+
Call Recording
```

we want:

```text
Business Communication Platform
+
AI Employees
+
Automation
+
CRM-like capabilities
+
Analytics
+
Voice
+
Video
```

The platform should eventually allow a company to create an AI receptionist in minutes.

Example:

```text
Create Business
       ↓
Add Phone Number
       ↓
Create AI Receptionist
       ↓
Upload Business Information
       ↓
Configure Business Hours
       ↓
Configure Call Routing
       ↓
Publish
```

The system should then handle incoming calls automatically.

---

# 3. Core Problem

Traditional PBX systems are powerful but complicated.

A normal business may need technical knowledge to configure:

```text
SIP Trunk
DID
Extension
IVR
Ring Group
Queue
Business Hours
Call Recording
Voicemail
Call Routing
```

AI adds another layer of complexity:

```text
AI model
Prompt
Knowledge
Tools
Memory
Voice
Transcription
Function calling
Webhooks
```

Our platform solves this by creating a single abstraction:

```text
BUSINESS
    ↓
PHONE SYSTEM
    ↓
AI AGENTS
    ↓
AUTOMATIONS
```

---

# 4. Target Users

## 4.1 Small Businesses

Examples:

* Dental clinics
* Medical clinics
* Real-estate companies
* Restaurants
* Salons
* Law firms
* Educational institutes
* Repair businesses
* Travel agencies
* Call centers

They should be able to configure the system without a telecommunications engineer.

---

## 4.2 Medium Businesses

Businesses with:

* Multiple departments
* Multiple phone numbers
* Multiple locations
* Multiple employees
* Call queues
* Call recording
* Customer support
* Sales teams

---

## 4.3 Developers

Developers should be able to:

* Self-host the platform
* Extend the system
* Create integrations
* Create custom AI tools
* Create custom automations
* Connect external CRMs
* Modify the frontend
* Modify the backend

---

## 4.4 System Administrators

Administrators should be able to:

* Manage PBX infrastructure
* Manage tenants
* Monitor servers
* Configure SIP
* Monitor calls
* Manage recordings
* Monitor AI services
* Review system health

---

# 5. Core Product Principles

## Principle 1 — Simple for Users

The user interface must hide unnecessary technical complexity.

Bad:

```text
PJSIP Transport
DTLS Fingerprint
RTP Symmetric
Rewrite Contact
Force RPort
```

Good:

```text
Enable Web Calling
☑

Use Secure Connection
☑
```

The system may internally configure the technical settings.

---

# 6. Principle 2 — AI First

AI should not be an add-on.

AI should be a first-class part of the architecture.

Traditional:

```text
PBX
 ├── Extension
 ├── IVR
 └── Queue
```

Our platform:

```text
PBX
 ├── Extension
 ├── IVR
 ├── Queue
 ├── AI Receptionist
 ├── AI Sales Agent
 ├── AI Support Agent
 └── AI Appointment Agent
```

---

# 7. Principle 3 — One Platform, Multiple Companies

The system must be multi-tenant from the beginning.

Architecture:

```text
Platform
│
├── Company A
│   ├── Users
│   ├── Numbers
│   ├── Extensions
│   ├── IVR
│   ├── AI Agents
│   ├── Automations
│   └── Recordings
│
├── Company B
│   ├── Users
│   ├── Numbers
│   ├── Extensions
│   ├── IVR
│   ├── AI Agents
│   └── ...
│
└── Company C
```

Every tenant must have strict data isolation.

---

# 8. Principle 4 — Self Hosted First

The initial platform should be designed to run without depending on proprietary infrastructure.

Preferred:

```text
Linux
Docker
PostgreSQL
Redis
FreePBX
Asterisk
Python
Laravel
Angular
Flutter
```

External paid services should be replaceable wherever practical.

---

# 9. Principle 5 — Free Software Where Possible

The development stack should prioritize:

* Open-source software
* Self-hosted services
* Free development tools
* Replaceable providers
* Vendor-neutral architecture

However:

> "Free platform" does NOT mean that real telephone calls, DID numbers, SIP trunks, cloud servers, or commercial AI APIs will always be free.

The architecture must distinguish between:

### Platform Costs

Software we control.

### Infrastructure Costs

Server, bandwidth, storage, etc.

### Provider Costs

DID, SIP trunk, SMS, AI APIs, etc.

---

# 10. Core Architecture Vision

The platform is divided into major planes.

```text
                         AI-PBX PLATFORM
                               │
       ┌───────────────────────┼───────────────────────┐
       │                       │                       │
       ▼                       ▼                       ▼
 USER PLANE              CONTROL PLANE            AI PLANE
       │                       │                       │
   Flutter                  Laravel                Python
   Softphone                API                    AI
       │                       │                       │
       └───────────────────────┼───────────────────────┘
                               │
                               ▼
                        TELEPHONY PLANE
                               │
                        FreePBX/Asterisk
                               │
                         SIP / WebRTC
                               │
                               ▼
                            DID/TRUNK
```

---

# 11. Main Components

## 11.1 FreePBX / Asterisk

Responsible for telephony.

Responsibilities include:

* SIP
* PJSIP
* Extensions
* DID
* IVR
* Queues
* Ring groups
* Call routing
* Voicemail
* Conferences
* Call recording
* RTP
* WebRTC support

FreePBX/Asterisk is the telephony engine.

It is NOT the application's main business database.

---

# 12. Laravel Middleware

Laravel is the **Control Plane**.

It manages:

* Authentication
* Authorization
* Companies
* Users
* Roles
* Extensions
* Phone numbers
* AI agents
* Knowledge bases
* Automations
* Call metadata
* Recordings metadata
* API access
* Configuration
* Tenant isolation

Laravel should not become the actual PBX engine.

---

# 13. Python AI Service

Python is the **AI Plane**.

It handles:

* AI voice agents
* AI orchestration
* Speech processing
* AI tools
* Knowledge retrieval
* Agent instructions
* Conversation context
* Call summaries
* Transcription
* AI analytics
* Future AI video integration

The AI layer should be independently scalable.

---

# 14. Angular Admin Portal

Angular is the primary administration interface.

It should provide:

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
Knowledge
Automations
Calls
Recordings
Transcripts
Analytics
System Settings
```

The frontend must communicate with Laravel APIs.

It must NOT directly modify the PBX.

---

# 15. Flutter Softphone

Flutter provides the employee/mobile communication application.

Core functionality:

```text
Login
SIP Registration
Dial Pad
Incoming Calls
Outgoing Calls
Active Call
Mute
Speaker
Hold
Transfer
Conference
Call History
Contacts
Voicemail
Presence
Recordings
```

Flutter should communicate through defined backend/SIP interfaces.

Business logic must not be placed directly inside UI widgets.

---

# 16. AI Receptionist

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
   ↓
Answer / Transfer / Book / Message
```

The AI receptionist should be able to:

* Greet callers
* Understand natural language
* Answer FAQs
* Explain business services
* Provide business hours
* Take messages
* Transfer calls
* Book appointments
* Collect lead information
* Create CRM records
* Trigger automations
* Summarize conversations

---

# 17. AI Agent Matrix

AI must not be designed as one giant agent.

The platform should support multiple specialized agents.

Example:

```text
AI AGENT MATRIX

Receptionist
     │
     ├── FAQ
     ├── Transfer
     └── Message

Sales Agent
     │
     ├── Lead Qualification
     ├── Product Information
     └── Follow-up

Support Agent
     │
     ├── Troubleshooting
     ├── Ticket Creation
     └── Escalation

Appointment Agent
     │
     ├── Availability
     ├── Booking
     └── Confirmation
```

Each agent should have:

```text
Identity
Purpose
Personality
Instructions
Knowledge
Tools
Permissions
Voice
Language
Business
Department
```

---

# 18. AI Agent Tools

Agents should interact with controlled tools instead of having unrestricted access to the system.

Example:

```text
AI Agent
   │
   ├── check_business_hours()
   ├── search_customer()
   ├── create_lead()
   ├── book_appointment()
   ├── transfer_call()
   ├── send_message()
   └── create_ticket()
```

Every tool must have:

* Authentication
* Authorization
* Tenant validation
* Input validation
* Logging
* Error handling

---

# 19. Visual IVR

The IVR should eventually become a visual flow builder.

Example:

```text
Incoming Call
      │
      ▼
Business Hours
   ┌──┴──┐
 Open  Closed
   │      │
   ▼      ▼
 IVR     AI
   │
 ┌─┼─────┐
 ▼ ▼     ▼
1  2     3
Sales Support Billing
```

Users should configure call flows visually rather than editing Asterisk configuration files.

---

# 20. Visual Automation

The platform should provide a simple automation engine.

Basic structure:

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
Send Notification
```

Possible triggers:

* Incoming call
* Missed call
* Completed call
* AI transfer
* Appointment booked
* New lead
* New voicemail
* Customer created

Possible actions:

* Create lead
* Send email
* Send SMS
* Send webhook
* Create appointment
* Notify employee
* Transfer call
* Add tag
* Update customer
* Start another workflow

---

# 21. Recording and Transcription

The platform should support:

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

Call records should contain:

```text
Caller
Receiver
Tenant
Direction
Start Time
End Time
Duration
Status
Recording
Transcript
Summary
AI Agent
Disposition
```

Recording access must always respect tenant permissions.

---

# 22. AI Analytics

The system should eventually analyze calls.

Possible metrics:

```text
Call Duration
Call Outcome
Customer Sentiment
AI Performance
Agent Performance
Lead Quality
Transfer Rate
Missed Call Rate
Resolution Rate
```

Example:

```text
100 Calls
│
├── 70 Resolved by AI
├── 20 Transferred
└── 10 Abandoned
```

---

# 23. AI Video Receptionist

This is a future capability.

The same AI brain should support multiple interfaces.

```text
                   AI AGENT
                       │
          ┌────────────┼────────────┐
          │            │            │
        Voice        Chat         Video
          │            │            │
         SIP          Web       WebRTC
```

The video receptionist may eventually include:

* AI avatar
* Lip synchronization
* Facial expressions
* Real-time conversation
* Business knowledge
* Screen sharing
* Appointment booking
* Human escalation

This feature must not delay the core MVP.

---

# 24. Scalability Vision

The system must eventually support:

```text
1 Company
      ↓
10 Companies
      ↓
100 Companies
      ↓
1,000+ Companies
```

The architecture must avoid assumptions that only one company exists.

Services should be independently scalable.

Example:

```text
                 Load Balancer
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
      Laravel 1    Laravel 2    Laravel 3
                       │
                    Redis
                       │
              ┌────────┴────────┐
              ▼                 ▼
          AI Worker 1       AI Worker 2
```

---

# 25. Security Vision

Security is a first-class requirement.

The platform must implement:

* Authentication
* Authorization
* Role-based access
* Tenant isolation
* API authentication
* Secret management
* HTTPS
* SIP security
* Rate limiting
* Input validation
* Audit logs
* Recording access control
* Secure file storage

Never expose:

```text
API keys
PBX passwords
SIP passwords
Database passwords
Storage credentials
```

to the frontend.

---

# 26. Observability

Production systems must be observable.

Eventually provide:

```text
System Health
PBX Health
SIP Health
AI Health
Database Health
Redis Health
Storage Health
API Health
Worker Health
```

Logging must allow engineers to trace:

```text
Call ID
 ↓
Tenant ID
 ↓
PBX Event
 ↓
AI Session
 ↓
AI Tool
 ↓
Automation
 ↓
Final Outcome
```

---

# 27. Developer Experience

The project is intended to be heavily AI-assisted / vibe-coded.

Therefore:

> Documentation is part of the architecture.

AI coding agents must have enough information to understand:

* Architecture
* Naming conventions
* Database structure
* API contracts
* Module boundaries
* Security requirements
* Testing rules
* Deployment rules

No AI coding agent should make architectural decisions blindly.

---

# 28. ADHD-Friendly Development

The project must be developed using small, measurable tasks.

Never create tasks such as:

```text
Build the entire AI PBX.
```

Instead:

```text
Sprint 1
 ├── Install Asterisk
 ├── Create Extension
 ├── Register SIP Client
 ├── Make Internal Call
 └── Verify Audio
```

Every task should have:

```text
Goal
Subtasks
Definition of Done
Test
Documentation
Commit
```

Development should proceed in one clear priority at a time.

---

# 29. MVP Definition

The first MVP is NOT the complete vision.

MVP includes:

```text
✓ FreePBX/Asterisk
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
✓ Multi-tenancy
✓ AI Receptionist
✓ AI FAQ
✓ AI Transfer
✓ AI Call Summary
```

---

# 30. Features Explicitly Deferred From MVP

The following must NOT delay the first MVP:

```text
✗ AI Video Avatar
✗ Advanced CRM
✗ Marketplace
✗ Billing Platform
✗ WhatsApp ecosystem
✗ Advanced Fine-tuning
✗ Kubernetes
✗ Multi-region deployment
✗ Advanced AI analytics
✗ Dozens of AI agents
```

These can be added after the foundation is stable.

---

# 31. Competitive Strategy

We are not trying to copy 3CX feature-by-feature.

We want to compete through:

```text
Traditional PBX
      +
Modern UX
      +
AI
      +
Automation
      +
Open/self-hosted architecture
```

The strongest differentiators should eventually be:

### 1. AI-native

AI is part of the phone system rather than an external integration.

### 2. Visual configuration

Business owners configure the system through visual workflows.

### 3. Multi-tenant

One infrastructure can serve multiple companies.

### 4. AI employees

Companies can create specialized AI agents.

### 5. Automation

Calls automatically trigger business processes.

### 6. Flutter softphone

One modern cross-platform application.

### 7. Self-hosting

Organizations can control their infrastructure and data.

---

# 32. Product Philosophy

The platform follows this rule:

> **Hide complexity, don't remove capability.**

Advanced users should still be able to access advanced configuration.

For example:

```text
Simple Mode
    ↓
Business Hours
    ↓
AI Receptionist
    ↓
Transfer
```

Advanced Mode:

```text
PJSIP
Dialplan
RTP
SIP Headers
Codec Configuration
Webhooks
Custom Contexts
```

The beginner should never be forced into Advanced Mode.

---

# 33. Source Project Strategy

The existing:

`kirklandsig/AIReceptionist`

repository is considered a **reference/prototype/integration source**, not the entire final platform.

We should evaluate and reuse suitable concepts/components while maintaining our own architecture.

Before copying or modifying code from external repositories:

1. Check license.
2. Check dependencies.
3. Check security.
4. Check maintenance status.
5. Check architectural compatibility.
6. Document what was reused.
7. Preserve required license notices.

The AIReceptionist repository is licensed under AGPL-3.0, so its license obligations must be considered before directly incorporating or distributing modified code.

---

# 34. Technology Philosophy

Use technology because it solves a problem.

Do NOT use:

```text
Go
Kubernetes
Microservices
Kafka
GraphQL
AI fine-tuning
```

simply because they are popular.

Initial architecture should remain simple.

Preferred initial stack:

```text
Flutter
Angular
Laravel
Python
PostgreSQL
Redis
FreePBX
Asterisk
Docker
```

Additional technologies are introduced only when justified.

---

# 35. Initial Repository Structure

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

# 36. Development Lifecycle

Every feature follows:

```text
IDEA
 ↓
REQUIREMENTS
 ↓
ARCHITECTURE
 ↓
DATABASE/API
 ↓
IMPLEMENTATION
 ↓
TEST
 ↓
DOCUMENT
 ↓
REVIEW
 ↓
COMMIT
```

Never:

```text
IDEA
 ↓
Vibe code everything
 ↓
Hope it works
```

---

# 37. Definition of Done

A feature is NOT complete merely because it works on the developer's machine.

A feature is complete when:

```text
✓ Code implemented
✓ Error handling implemented
✓ Security reviewed
✓ Tests added
✓ Tenant isolation verified
✓ API documented
✓ UI completed
✓ Logging implemented where needed
✓ Documentation updated
✓ Manual test completed
✓ Git commit created
```

---

# 38. Long-Term Vision

The ultimate platform should allow a business owner to say:

> "I need a phone system."

And within minutes configure:

```text
Company
 ↓
Phone Number
 ↓
Departments
 ↓
Employees
 ↓
AI Receptionist
 ↓
AI Sales Agent
 ↓
AI Support Agent
 ↓
IVR
 ↓
Automations
```

The business owner should then be able to see:

```text
Today's Calls
Today's Leads
Missed Calls
AI Resolutions
Appointments
Transfers
Revenue Opportunities
```

without understanding how Asterisk, SIP, RTP, AI models, or backend services work.

---

# 39. Ultimate Product

The final vision is:

```text
                         AI-PBX
                           │
          ┌────────────────┼────────────────┐
          │                │                │
       TELEPHONY          AI             BUSINESS
          │                │                │
       SIP/DID          Agents         Automation
       IVR              Voice          CRM
       Queue            Video          Leads
       Extension        Chat           Calendar
       Recording        Knowledge      Analytics
          │                │                │
          └────────────────┼────────────────┘
                           │
                    ONE PLATFORM
                           │
                 MULTIPLE COMPANIES
```

The platform should evolve from:

> **PBX**

into:

> **AI-powered Business Communication Operating System.**

---

# 40. Golden Rule

Every architectural, coding, and product decision should be evaluated against this question:

> **Does this make the system more powerful without making it unnecessarily harder for the business owner, developer, or administrator to use?**

If the answer is **yes**, proceed.

If the answer is **no**, reconsider the design.

---

# 41. Current Priority

The current priority is NOT AI Video.

The current priority is NOT advanced automation.

The current priority is NOT defeating every feature of 3CX.

The current priority is:

```text
PHASE 1

Reliable PBX
     ↓
Reliable SIP
     ↓
Flutter Phone
     ↓
Laravel Control Plane
     ↓
Angular Admin
     ↓
Multi-Tenant Foundation
     ↓
AI Receptionist
```

Everything else comes afterward.

---

# 42. Success Criteria

The first major success milestone is:

> **A real company can receive a call through a DID, have the call handled by the AI receptionist, transfer the caller to a human using the Flutter softphone, record the call, store the transcript, and view the complete interaction from the Angular Admin Portal — while multiple companies can use the same platform without accessing each other's data.**

Once this works reliably:

**The platform has a real foundation.**

---

# 43. Related Project Documents

This document should be read before:

```text
02-ARCHITECTURE.md
03-PREREQUISITES.md
04-SPRINT-ROADMAP.md
05-VIBE-CODING-RULES.md
```

Recommended reading order:

```text
01 PROJECT VISION
       ↓
02 ARCHITECTURE
       ↓
03 PREREQUISITES
       ↓
04 SPRINT ROADMAP
       ↓
05 VIBE CODING RULES
```

These five documents collectively define the initial engineering contract for the project.
