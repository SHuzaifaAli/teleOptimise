# 04 — Sprint Roadmap

## 1. Purpose

This document defines the implementation roadmap for the AI-PBX Platform.

The project will be developed incrementally.

Each sprint must produce something that can be:

* built
* tested
* demonstrated
* reviewed
* committed to Git

Do not build the entire platform at once.

---

# 2. Development Philosophy

The platform follows this order:

```text
Infrastructure
      ↓
PBX
      ↓
SIP
      ↓
Flutter Softphone
      ↓
Backend
      ↓
Admin Portal
      ↓
Multi-Tenancy
      ↓
IVR
      ↓
Recording
      ↓
AI Receptionist
      ↓
AI Tools
      ↓
Automation
      ↓
Analytics
      ↓
Scale
      ↓
AI Video
```

### Golden Rule

> Never build an advanced feature on top of an untested foundation.

---

# 3. Overall Roadmap

| Phase    | Sprints | Main Goal                   |
| -------- | ------: | --------------------------- |
| Phase 0  |       0 | Project setup               |
| Phase 1  |     1–3 | PBX + SIP foundation        |
| Phase 2  |     4–5 | Flutter Softphone           |
| Phase 3  |     6–8 | Laravel + Database          |
| Phase 4  |    9–10 | Angular Admin               |
| Phase 5  |   11–13 | Multi-Tenancy               |
| Phase 6  |   14–16 | IVR + Call Flows            |
| Phase 7  |   17–18 | Recording + Call History    |
| Phase 8  |   19–22 | AI Receptionist             |
| Phase 9  |   23–25 | AI Tools + Knowledge        |
| Phase 10 |   26–28 | Automations                 |
| Phase 11 |   29–30 | Analytics                   |
| Phase 12 |   31–34 | Production Hardening        |
| Phase 13 |     35+ | Scaling + Advanced Features |

Estimated MVP:

```text
~22–25 sprints
```

If one sprint is approximately one week:

```text
~5–6 months
```

With multiple developers/AI coding agents working in parallel, the calendar time can be reduced.

The roadmap is based on deliverables, not arbitrary dates.

---

# 4. Sprint 0 — Project Initialization

## Goal

Create the development foundation.

### Tasks

```text
[ ] Create Git repository
[ ] Create repository structure
[ ] Create README
[ ] Add architecture documentation
[ ] Add prerequisite documentation
[ ] Configure Git branches
[ ] Add .gitignore files
[ ] Create environment templates
[ ] Configure Docker
[ ] Create development documentation
```

### Repository

Recommended:

```text
ai-pbx/
├── backend/
├── ai-service/
├── admin/
├── mobile/
├── infrastructure/
├── docs/
└── scripts/
```

### Deliverable

A clean repository that every developer/AI agent can clone and understand.

### Exit Criteria

```text
[ ] Repository works
[ ] Docker starts
[ ] Documentation exists
[ ] No secrets committed
[ ] Basic CI validation exists
```

---

# 5. Sprint 1 — Asterisk / FreePBX Foundation

## Goal

Create the first working PBX.

### Tasks

```text
[ ] Install FreePBX
[ ] Install/configure Asterisk
[ ] Configure PJSIP
[ ] Create test extensions
[ ] Configure basic dialplan
[ ] Test extension-to-extension calls
[ ] Test DTMF
[ ] Test audio
```

### Test

```text
Extension 1001
      ↕
Extension 1002
```

Expected:

```text
1001 calls 1002
1002 answers
Two-way audio works
Call terminates correctly
```

### Exit Criteria

```text
[ ] SIP registration works
[ ] Calls connect
[ ] Two-way audio works
[ ] DTMF works
[ ] No major NAT/audio issues
```

---

# 6. Sprint 2 — SIP Trunk + DID

## Goal

Connect the PBX to real telephone networks.

### Tasks

```text
[ ] Select SIP provider
[ ] Configure trunk
[ ] Configure DID
[ ] Configure inbound route
[ ] Configure outbound route
[ ] Test inbound call
[ ] Test outbound call
[ ] Test caller ID
[ ] Test DTMF
```

### Call Flow

```text
Mobile Phone
     ↓
PSTN
     ↓
SIP Provider
     ↓
Asterisk
     ↓
Extension
```

### Exit Criteria

```text
[ ] External inbound call works
[ ] External outbound call works
[ ] Two-way audio works
[ ] Caller ID works
[ ] Call termination works
```

---

# 7. Sprint 3 — SIP/WebRTC Foundation

## Goal

Prepare the platform for modern clients.

### Tasks

```text
[ ] Configure SIP over WebSocket where required
[ ] Configure TLS
[ ] Configure WebRTC
[ ] Configure ICE
[ ] Configure STUN
[ ] Evaluate TURN requirement
[ ] Test browser/WebRTC endpoint
[ ] Document network requirements
```

### Exit Criteria

```text
[ ] WebRTC endpoint registers
[ ] Calls work
[ ] Audio works
[ ] NAT scenarios documented
[ ] Required ports documented
```

---

# 8. Sprint 4 — Flutter SIP Core

## Goal

Create the first Flutter softphone.

### Screens

```text
Login
Dial Pad
Call Screen
Call History
Settings
```

### Tasks

```text
[ ] Create Flutter project
[ ] Configure environments
[ ] Create SIP abstraction
[ ] Connect SIP registration
[ ] Show registration state
[ ] Implement dial pad
[ ] Implement outgoing call
[ ] Implement incoming call
```

### Architecture

```text
SIP Engine
    ↓
SipService
    ↓
SipRepository
    ↓
CallBloc
    ↓
UI
```

Do not place SIP logic directly inside widgets.

### Exit Criteria

```text
[ ] Flutter registers
[ ] Flutter calls extension
[ ] Flutter receives call
[ ] Two-way audio works
```

---

# 9. Sprint 5 — Flutter Calling Features

## Goal

Make the softphone usable.

### Tasks

```text
[ ] Mute
[ ] Speaker
[ ] Hold
[ ] Resume
[ ] Transfer
[ ] DTMF
[ ] Call timer
[ ] Call states
[ ] Call history
[ ] Missed calls
[ ] Error handling
```

### Call State Machine

```text
idle
 ↓
calling
 ↓
ringing
 ↓
connected
 ↓
on_hold
 ↓
connected
 ↓
completed
```

Additional states:

```text
failed
missed
transferred
rejected
```

### Exit Criteria

A developer can use Flutter as a basic production-style SIP phone.

---

# 10. Sprint 6 — Laravel Foundation

## Goal

Create the application control plane.

### Tasks

```text
[ ] Create Laravel project
[ ] Configure PostgreSQL
[ ] Configure Redis
[ ] Configure environment
[ ] Create API structure
[ ] Authentication
[ ] User model
[ ] Role model
[ ] Tenant model
[ ] API versioning
```

API:

```text
/api/v1/
```

### Exit Criteria

```text
[ ] Laravel runs
[ ] Database works
[ ] Authentication works
[ ] API versioning works
[ ] Tests exist
```

---

# 11. Sprint 7 — Database Core

## Goal

Build the core data model.

### Initial entities

```text
Tenant
Business
User
Role
Permission
Extension
PhoneNumber
Department
```

Relationship:

```text
Tenant
 ├── Businesses
 ├── Users
 ├── Extensions
 ├── Phone Numbers
 └── Departments
```

### Tasks

```text
[ ] Create migrations
[ ] Create models
[ ] Create relationships
[ ] Add tenant_id
[ ] Add indexes
[ ] Add constraints
[ ] Create factories
[ ] Create seeders
[ ] Add tests
```

### Exit Criteria

Database can represent multiple businesses without mixing their data.

---

# 12. Sprint 8 — PBX Control Layer

## Goal

Connect Laravel to PBX functionality.

### Tasks

```text
[ ] Define PBX adapter
[ ] Define extension provisioning
[ ] Define DID provisioning
[ ] Define department provisioning
[ ] Define IVR provisioning interface
[ ] Define call event ingestion
[ ] Define PBX authentication
```

Architecture:

```text
Laravel
   ↓
PBX Adapter
   ↓
FreePBX/Asterisk
```

Never expose PBX credentials to Flutter.

### Exit Criteria

Laravel can safely request supported PBX operations.

---

# 13. Sprint 9 — Angular Foundation

## Goal

Create the administration portal.

### Screens

```text
Login
Dashboard
Businesses
Users
Extensions
Phone Numbers
Departments
Settings
```

### Tasks

```text
[ ] Create Angular project
[ ] Configure routing
[ ] Configure authentication
[ ] Configure API client
[ ] Create layout
[ ] Create navigation
[ ] Create reusable components
[ ] Create error handling
```

### Exit Criteria

Admin can log in and communicate with Laravel.

---

# 14. Sprint 10 — Admin CRUD

## Goal

Allow administrators to configure the PBX.

### Features

```text
[ ] Create business
[ ] Edit business
[ ] Create user
[ ] Assign role
[ ] Create extension
[ ] Assign extension
[ ] Configure phone number
[ ] Configure department
```

### Exit Criteria

A basic business can be created entirely through the Admin Portal.

---

# 15. Sprint 11 — Multi-Tenant Security

## Goal

Make tenant isolation production-safe.

### Tasks

```text
[ ] Tenant middleware
[ ] Authorization policies
[ ] Tenant-scoped repositories
[ ] Tenant-scoped queries
[ ] Tenant-scoped files
[ ] Tenant-scoped AI configuration
[ ] Tenant-scoped calls
[ ] Tenant-scoped recordings
```

### Security Test

```text
Tenant A
   ❌
   ↓
Tenant B data
```

Must always fail.

### Exit Criteria

Cross-tenant access tests pass.

---

# 16. Sprint 12 — Tenant Phone Routing

## Goal

Connect DID routing to tenants.

### Flow

```text
Incoming DID
      ↓
Asterisk
      ↓
DID Resolver
      ↓
Tenant
      ↓
Business
      ↓
Call Flow
```

### Tasks

```text
[ ] DID mapping
[ ] Tenant resolver
[ ] Business resolver
[ ] Default call flow
[ ] Invalid DID handling
[ ] Unknown tenant handling
```

### Exit Criteria

Multiple businesses can receive calls through the same platform.

---

# 17. Sprint 13 — Call Flow Engine

## Goal

Create the foundation for configurable call routing.

Instead of hardcoding:

```text
if number == X
```

create a call-flow model.

Example:

```text
Incoming Call
      ↓
Business Hours?
   ↙       ↘
 Yes       No
 ↓          ↓
IVR       Voicemail
```

### Node Types

Initial:

```text
Start
Business Hours
IVR
Extension
Department
Ring Group
Queue
Voicemail
Transfer
Hangup
AI Agent
```

### Exit Criteria

A call flow can be represented as structured data.

---

# 18. Sprint 14 — IVR

## Goal

Build a professional IVR system.

### Example

```text
Welcome to Company X.

Press 1 for Sales
Press 2 for Support
Press 3 for Billing
Press 0 for Reception
```

### Tasks

```text
[ ] IVR model
[ ] IVR configuration API
[ ] Admin UI
[ ] DTMF routing
[ ] Timeout
[ ] Invalid input
[ ] Retry limit
[ ] Business hours
[ ] After-hours
[ ] Language support
```

### Exit Criteria

Admin can configure an IVR without editing Asterisk files manually.

---

# 19. Sprint 15 — Advanced Call Routing

## Goal

Build professional PBX routing features.

### Features

```text
[ ] Ring groups
[ ] Queues
[ ] Department routing
[ ] Time conditions
[ ] Holiday routing
[ ] Call forwarding
[ ] Call transfer
[ ] Voicemail
```

### Exit Criteria

The platform can handle common business telephone workflows.

---

# 20. Sprint 16 — Call Recording

## Goal

Implement reliable call recording.

### Flow

```text
Call
 ↓
Asterisk
 ↓
Recording
 ↓
Object Storage
 ↓
Metadata
 ↓
PostgreSQL
```

### Tasks

```text
[ ] Recording configuration
[ ] Recording lifecycle
[ ] Storage adapter
[ ] Upload
[ ] Metadata
[ ] Access control
[ ] Retention policy
[ ] Delete policy
```

### Exit Criteria

Authorized users can find and play recordings.

---

# 21. Sprint 17 — Call History

## Goal

Create a complete call history system.

### Call Record

```text
Call ID
Tenant
Business
Caller
Callee
DID
Direction
Status
Start time
Answer time
End time
Duration
Agent
Recording
Transcript
```

### Features

```text
[ ] Incoming calls
[ ] Outgoing calls
[ ] Missed calls
[ ] Failed calls
[ ] Transferred calls
[ ] Search
[ ] Filters
[ ] Date range
```

### Exit Criteria

Admin can investigate a call from beginning to end.

---

# 22. Sprint 18 — AI Service Foundation

## Goal

Create the Python AI service.

### Architecture

```text
Laravel
    ↓
AI API
    ↓
Python
    ↓
AI Agent
```

### Tasks

```text
[ ] Python project
[ ] API server
[ ] Configuration
[ ] AI provider adapter
[ ] Agent model
[ ] Session model
[ ] Tool interface
[ ] Logging
[ ] Health endpoint
```

### Exit Criteria

Python AI service can start and respond to a controlled test request.

---

# 23. Sprint 19 — AI Receptionist MVP

## Goal

Make an AI agent answer telephone calls.

### Call Flow

```text
DID
 ↓
Asterisk
 ↓
AI Receptionist
 ↓
Customer
```

### Agent capabilities

Initial:

```text
[ ] Greeting
[ ] Natural conversation
[ ] Business FAQs
[ ] Business hours
[ ] Location
[ ] Services
[ ] Basic information
[ ] Goodbye
```

### Exit Criteria

A real caller can speak to the AI receptionist and receive useful answers.

---

# 24. Sprint 20 — AI Transfer

## Goal

Allow AI to transfer callers to humans.

Example:

```text
Customer:
"I need to speak to sales."

AI:
"I'll connect you with sales."

        ↓

Sales Queue
```

### Tasks

```text
[ ] Transfer tool
[ ] Department lookup
[ ] Extension lookup
[ ] Queue transfer
[ ] Human escalation
[ ] Transfer failure
[ ] AI fallback
```

### Exit Criteria

AI can successfully transfer calls.

---

# 25. Sprint 21 — AI Tools

## Goal

Give AI controlled business capabilities.

Tools:

```text
get_business_hours()
get_services()
get_customer()
create_lead()
create_message()
book_appointment()
cancel_appointment()
transfer_call()
```

### Important

AI must never execute arbitrary database operations.

Correct:

```text
AI
 ↓
Tool
 ↓
Authorization
 ↓
Tenant Validation
 ↓
Business Logic
 ↓
Result
```

### Exit Criteria

Every AI action is controlled and auditable.

---

# 26. Sprint 22 — Knowledge Base

## Goal

Allow businesses to train/configure their AI receptionist using business information.

### Sources

```text
FAQs
Documents
PDFs
Website
Policies
Services
Products
```

### Pipeline

```text
Source
 ↓
Parser
 ↓
Chunking
 ↓
Embedding
 ↓
Vector Storage
 ↓
Retrieval
 ↓
AI
```

### Admin Features

```text
[ ] Upload document
[ ] Delete document
[ ] View processing status
[ ] Add FAQ
[ ] Edit FAQ
[ ] Search knowledge
```

### Exit Criteria

AI can answer questions from tenant-specific knowledge.

---

# 27. Sprint 23 — AI Call Summary

## Goal

Automatically summarize calls.

Example output:

```text
Customer:
John

Reason:
Asked about vehicle insurance.

Outcome:
Requested callback.

Action:
Sales team should contact customer.
```

### Tasks

```text
[ ] Transcript
[ ] Summary
[ ] Sentiment
[ ] Intent
[ ] Action items
[ ] Save metadata
```

### Exit Criteria

Every eligible AI call can produce a useful summary.

---

# 28. Sprint 24 — AI Analytics

## Goal

Turn conversations into business intelligence.

Metrics:

```text
Total calls
AI calls
Human calls
Transferred calls
Missed calls
Average duration
AI resolution rate
Transfer rate
Customer intent
Common questions
```

### Exit Criteria

Admin can understand how calls are performing.

---

# 29. Sprint 25 — Automation Engine

## Goal

Create configurable business automations.

Architecture:

```text
Trigger
   ↓
Conditions
   ↓
Actions
```

### Example

```text
Missed Call
    ↓
Caller is customer?
    ↓
Yes
    ↓
Send SMS
    ↓
Create CRM activity
```

### Triggers

```text
[ ] Incoming call
[ ] Missed call
[ ] Completed call
[ ] AI transfer
[ ] New lead
[ ] Appointment
[ ] Schedule
[ ] Webhook
```

### Actions

```text
[ ] Send email
[ ] Send SMS
[ ] Create lead
[ ] Create task
[ ] Webhook
[ ] AI action
```

### Exit Criteria

Admin can create an automation without developer intervention.

---

# 30. Sprint 26 — Agent Matrix

## Goal

Allow businesses to use multiple specialized AI agents.

Example:

```text
                  AI Receptionist
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
       Sales AI       Support AI     Booking AI
```

Each agent can have:

```text
Personality
Instructions
Knowledge
Tools
Voice
Permissions
Transfer rules
```

### Exit Criteria

A business can configure multiple AI agents.

---

# 31. Sprint 27 — AI Business Growth

## Goal

Turn call data into actionable business insights.

Examples:

```text
"Customers frequently ask about pricing."

"35% of calls request appointments."

"Support calls peak between 5 PM and 8 PM."

"20 missed calls occurred outside business hours."
```

Potential recommendations:

```text
[ ] Staffing recommendations
[ ] FAQ recommendations
[ ] Sales opportunities
[ ] Missed-call opportunities
[ ] Business-hour recommendations
```

---

# 32. Sprint 28 — Admin UX Improvement

## Goal

Make the system significantly easier than traditional PBX administration.

### Design Principle

Do not make users understand:

```text
Asterisk
Dialplan
SIP headers
PJSIP endpoints
RTP
```

Instead show:

```text
Who calls?
      ↓
What should happen?
      ↓
Who should answer?
      ↓
What happens if nobody answers?
```

### Features

```text
[ ] Visual call-flow builder
[ ] Drag/drop nodes
[ ] Preview
[ ] Validation
[ ] Templates
[ ] Test flow
[ ] Publish flow
[ ] Rollback
```

---

# 33. Sprint 29 — Security Hardening

## Goal

Prepare for real-world deployment.

### Tasks

```text
[ ] RBAC audit
[ ] Tenant isolation audit
[ ] API rate limiting
[ ] Input validation
[ ] File validation
[ ] Secret management
[ ] Security headers
[ ] Audit logs
[ ] Session security
[ ] SIP security
[ ] Firewall review
```

### Security Tests

```text
[ ] Cross-tenant API attack
[ ] Invalid token
[ ] Expired token
[ ] Privilege escalation
[ ] File upload abuse
[ ] SQL injection
[ ] Rate-limit bypass
```

---

# 34. Sprint 30 — Production Hardening

## Goal

Make the platform deployable.

### Infrastructure

```text
[ ] Production Docker setup
[ ] Reverse proxy
[ ] HTTPS
[ ] Database backup
[ ] Recording backup
[ ] Monitoring
[ ] Logging
[ ] Health checks
[ ] Alerting
```

### Failure Testing

```text
[ ] AI unavailable
[ ] Database unavailable
[ ] Redis unavailable
[ ] PBX unavailable
[ ] SIP provider unavailable
[ ] Internet interruption
[ ] Storage unavailable
```

### Exit Criteria

The platform fails gracefully instead of crashing unpredictably.

---

# 35. Sprint 31 — Performance Testing

## Goal

Determine actual capacity.

Test:

```text
10 concurrent calls
25 concurrent calls
50 concurrent calls
100 concurrent calls
```

Measure:

```text
CPU
RAM
Network
RTP
SIP latency
AI latency
Database load
Redis load
Recording throughput
```

Do not claim scalability without measurements.

---

# 36. Sprint 32 — Horizontal Scaling

## Goal

Prepare for multiple servers.

Potential architecture:

```text
                 Load Balancer
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
      Laravel 1    Laravel 2    Laravel 3
          │            │            │
          └────────────┼────────────┘
                       ↓
                  PostgreSQL
                       +
                     Redis
```

AI:

```text
AI Worker 1
AI Worker 2
AI Worker 3
```

PBX can be scaled separately.

---

# 37. Sprint 33 — Observability

## Goal

Understand the platform in production.

Track:

```text
API metrics
PBX metrics
SIP metrics
Call metrics
AI metrics
Database metrics
Infrastructure metrics
```

Add:

```text
[ ] Structured logs
[ ] Correlation IDs
[ ] Request IDs
[ ] Call IDs
[ ] Error tracking
[ ] Metrics
[ ] Alerts
```

---

# 38. Sprint 34 — MVP Release

## MVP Definition

The MVP is complete when a business can:

```text
1. Create account
2. Create business
3. Configure users
4. Configure extensions
5. Configure DID
6. Configure IVR
7. Configure call routing
8. Receive calls
9. Make calls
10. Record calls
11. View call history
12. Configure AI receptionist
13. Let AI answer calls
14. Let AI answer FAQs
15. Transfer AI calls
16. View AI summary
17. Configure basic automation
```

---

# 39. Phase 2 — Advanced Features

After MVP stability:

```text
Advanced CRM
Advanced analytics
SMS
Email
Calendar
WhatsApp
AI sales agent
AI support agent
AI booking agent
Advanced automation
Mobile notifications
Advanced reporting
```

---

# 40. Phase 3 — AI Video

Only after the voice platform is stable.

Architecture:

```text
Flutter/Web
      ↓
WebRTC
      ↓
AI Video Session
      ↓
AI Avatar
      ↓
Speech + LLM + Vision
      ↓
Tools
```

Potential use cases:

```text
Virtual receptionist
Video support
Sales assistant
Appointment assistant
Kiosk assistant
```

---

# 41. Phase 4 — Enterprise Scaling

Future:

```text
Multi-region
High availability
PBX clustering
AI worker clusters
Database replicas
Object storage replication
Enterprise SSO
Advanced RBAC
Audit/compliance
Billing
Usage metering
```

---

# 42. Phase 5 — Platform / Marketplace

Long-term possibility:

```text
AI Agent Marketplace
Integration Marketplace
Voice Marketplace
Automation Marketplace
Third-party applications
Developer API
Webhooks
SDKs
```

---

# 43. Sprint Rules

Every sprint must have:

```text
Goal
Tasks
Dependencies
Deliverables
Tests
Exit Criteria
```

Never define a sprint only as:

```text
"Build AI receptionist"
```

Instead:

```text
AI Receptionist
 ├── Audio connection
 ├── Session management
 ├── Prompt
 ├── Knowledge
 ├── Tools
 ├── Transfer
 ├── Failure handling
 └── Tests
```

---

# 44. AI Coding Agent Rules

Every AI coding agent must follow:

```text
1. Read README.
2. Read architecture.
3. Read prerequisites.
4. Read sprint roadmap.
5. Identify current sprint.
6. Inspect existing code.
7. Do not rewrite unrelated code.
8. Implement smallest correct change.
9. Write tests.
10. Run tests.
11. Report files changed.
12. Report remaining issues.
```

AI agents must not invent infrastructure.

If something is missing:

```text
BLOCKED:
Missing SIP credentials.
```

is better than:

```text
Fake SIP implementation.
```

---

# 45. Sprint Branch Strategy

Recommended:

```text
main
develop

feature/sprint-01-pbx
feature/sprint-02-sip-trunk
feature/sprint-04-flutter-sip
feature/sprint-06-laravel
...
```

Feature branches should be short-lived.

---

# 46. Commit Strategy

Use meaningful commits.

Good:

```text
feat(pbx): configure test extensions
feat(sip): add registration service
feat(auth): add tenant authentication
feat(ai): add receptionist session
fix(call): handle transfer failure
test(tenant): prevent cross-tenant access
```

Avoid:

```text
update
changes
final
final2
new
working
```

---

# 47. Definition of Done

A feature is NOT done because the code exists.

It is done when:

```text
[ ] Code implemented
[ ] Architecture followed
[ ] Tests written
[ ] Tests pass
[ ] Error handling exists
[ ] Authorization exists
[ ] Tenant isolation checked
[ ] Logging exists where required
[ ] Documentation updated
[ ] No secrets committed
[ ] Manual test completed
```

---

# 48. Blocked Task Rule

If a task depends on unavailable infrastructure:

```text
DO NOT FAKE IT.
```

Example:

```text
DID unavailable
```

Allowed:

```text
Develop extension-to-extension calling.
```

Not allowed:

```text
Pretend DID works using hardcoded phone numbers.
```

---

# 49. Critical Path

The most important path is:

```text
FreePBX
   ↓
Asterisk
   ↓
SIP
   ↓
Flutter
   ↓
Laravel
   ↓
Angular
   ↓
Multi-Tenant
   ↓
IVR
   ↓
Recording
   ↓
AI
```

If this path works reliably, the rest of the platform can evolve around it.

---

# 50. What Can Be Developed in Parallel

Once the foundation is stable, work can be parallelized.

Example:

```text
                 ┌── Flutter
                 │
Asterisk ────────┼── Laravel
                 │
                 ├── Angular
                 │
                 └── Python AI
```

However, integration must always follow the agreed contracts.

---

# 51. Recommended Team / AI Agent Split

If multiple developers or coding agents are available:

### Agent 1 — Telephony

```text
FreePBX
Asterisk
SIP
PJSIP
RTP
IVR
```

### Agent 2 — Flutter

```text
SIP client
BLoC
Call UI
Notifications
Call history
```

### Agent 3 — Laravel

```text
API
Database
Authentication
Multi-tenancy
Authorization
```

### Agent 4 — Angular

```text
Admin portal
Forms
Dashboard
Call-flow builder
```

### Agent 5 — AI

```text
Python
AI agents
Tools
Knowledge
Realtime voice
Summaries
```

### Agent 6 — QA / DevOps

```text
Testing
Docker
CI/CD
Monitoring
Security
Performance
```

---

# 52. ADHD-Friendly Development Mode

The project should be managed in small visible tasks.

Do not work from:

```text
"Build the entire AI-PBX."
```

Work from:

```text
TODAY
↓
Current Sprint
↓
Current Task
↓
Current Subtask
↓
Test
↓
Done
```

Only one primary task should be active at a time when possible.

---

# 53. Daily Development Format

Recommended:

```text
TODAY

Sprint:
S04 — Flutter SIP Core

Primary Task:
SIP Registration

Subtasks:
[ ] Create SipService
[ ] Add credentials model
[ ] Connect registration
[ ] Listen for registration state
[ ] Update BLoC
[ ] Display status
[ ] Test

DONE WHEN:
Flutter shows "Registered".
```

This keeps the project manageable.

---

# 54. Release Milestones

## Milestone 1 — PBX

```text
Extension ↔ Extension
```

## Milestone 2 — Real Telephone

```text
PSTN ↔ SIP ↔ Asterisk
```

## Milestone 3 — Softphone

```text
Flutter ↔ SIP ↔ Asterisk
```

## Milestone 4 — Business Platform

```text
Angular ↔ Laravel ↔ PostgreSQL
```

## Milestone 5 — Multi-Tenant PBX

```text
Tenant A
Tenant B
Tenant C
```

## Milestone 6 — Configurable IVR

```text
DID → IVR → Department
```

## Milestone 7 — AI Receptionist

```text
DID → AI → Customer
```

## Milestone 8 — AI Employee

```text
AI
 ├── Answer
 ├── Understand
 ├── Search
 ├── Book
 ├── Transfer
 ├── Create lead
 └── Follow up
```

## Milestone 9 — Business Intelligence

```text
Calls
 ↓
AI Analysis
 ↓
Insights
 ↓
Automations
 ↓
Business Growth
```

---

# 55. Final Product Evolution

The product should evolve through these stages:

```text
PBX
 ↓
Cloud PBX
 ↓
Multi-Tenant PBX
 ↓
AI Receptionist
 ↓
AI Employee
 ↓
AI Business Platform
```

The long-term vision is not simply:

> "Asterisk with ChatGPT."

It is:

> **A programmable business communication platform where telephony, AI agents, workflows, knowledge, and business operations work together.**

---

# 56. Final Roadmap Rule

Do not optimize for the largest feature list.

Optimize for:

```text
Reliable
    ↓
Simple
    ↓
Configurable
    ↓
Multi-Tenant
    ↓
Intelligent
    ↓
Automated
    ↓
Scalable
```

The first objective is to make **one business work extremely well**.

Then make:

```text
1 business
   ↓
10 businesses
   ↓
100 businesses
   ↓
1000+ businesses
```

without rewriting the entire platform.
