# AI-PBX Platform — Architecture

**Document:** `02-ARCHITECTURE.md`
**Version:** 1.0
**Status:** Architecture Source of Truth
**Parent Document:** `01-PROJECT-VISION.md`

---

# 1. Purpose

This document defines the technical architecture of the AI-PBX platform.

It answers:

* What components exist?
* What does each component do?
* How do components communicate?
* Where does business logic live?
* Where does telephony logic live?
* Where does AI logic live?
* How is multi-tenancy enforced?
* How are calls routed?
* How do we scale?
* What technologies are allowed?
* What architectural decisions must not be violated?

---

# 2. Architecture Goal

The platform must be:

```text
Multi-Tenant
Self-Hostable
Scalable
Secure
AI-First
Provider-Agnostic
API-Driven
Modular
Observable
Easy to Configure
AI-Code Friendly
```

The architecture should support a small deployment:

```text
1 Server
1 Company
10 Extensions
```

and eventually:

```text
Multiple Servers
Multiple Companies
Thousands of Extensions
Large Call Volumes
Multiple AI Workers
```

without requiring a complete rewrite.

---

# 3. Architectural Model

The platform is divided into five major planes:

```text
                    AI-PBX PLATFORM
                           │
       ┌───────────────────┼───────────────────┐
       │                   │                   │
       ▼                   ▼                   ▼
   USER PLANE         CONTROL PLANE        AI PLANE
       │                   │                   │
    Flutter             Laravel              Python
   Softphone              API             AI Services
       │                   │                   │
       └───────────────────┼───────────────────┘
                           │
                           ▼
                    TELEPHONY PLANE
                           │
                    FreePBX / Asterisk
                           │
                      SIP / WebRTC
                           │
                           ▼
                       DID / TRUNK
                           │
                           ▼
                    PUBLIC TELEPHONE
                        NETWORK
```

A fifth plane handles infrastructure:

```text
                    INFRASTRUCTURE
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
         PostgreSQL      Redis        Object Storage
```

---

# 4. Component Responsibilities

## 4.1 Flutter

### Role

**User / Softphone Client**

### Responsible for

```text
Authentication
SIP Registration
Dial Pad
Incoming Calls
Outgoing Calls
Active Call UI
Mute
Speaker
Hold
Transfer
Conference
Contacts
Call History
Presence
Voicemail
```

### Must NOT own

```text
Tenant configuration
AI business logic
PBX configuration
Authorization rules
Database business logic
```

Flutter is a client.

The backend is the source of truth.

---

# 5. Angular

### Role

**Administration Client**

Angular provides:

```text
Dashboard
Company Management
User Management
Extensions
Phone Numbers
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
Settings
```

Angular communicates with Laravel.

```text
Angular
   │
 HTTPS
   │
   ▼
Laravel API
```

Angular must never directly modify the database.

Angular must never directly configure Asterisk.

---

# 6. Laravel

### Role

**Control Plane**

Laravel is the primary application backend.

Responsibilities:

```text
Authentication
Authorization
Tenant Management
User Management
Business Management
Extension Management
Phone Number Management
Department Management
AI Agent Configuration
Knowledge Management
Automation Configuration
Call Metadata
Recording Metadata
Audit Logs
API
Webhooks
System Configuration
```

Laravel coordinates other systems.

It does not replace Asterisk.

---

# 7. Python

### Role

**AI Plane**

Python handles AI-specific processing.

Responsibilities:

```text
AI Agents
Realtime AI
Speech Processing
AI Orchestration
Tool Calling
Knowledge Retrieval
Conversation Context
Call Summaries
Transcription
AI Analytics
AI Memory
Future AI Video
```

Python should expose an internal service API.

Example:

```text
Laravel
   │
   │ Internal API
   ▼
Python AI Service
```

Python should not become the application's general-purpose backend.

---

# 8. FreePBX / Asterisk

### Role

**Telephony Engine**

Asterisk is responsible for actual telecommunications.

```text
SIP
PJSIP
DID
Extensions
Dialplan
IVR
Queues
Ring Groups
Voicemail
Conference
Call Recording
RTP
WebRTC
```

FreePBX provides the administrative PBX layer.

Asterisk performs the underlying telephony processing.

---

# 9. PostgreSQL

### Role

**Application Source of Truth**

PostgreSQL stores platform/business data.

Examples:

```text
Tenants
Users
Roles
Businesses
Phone Numbers
Extensions
Departments
AI Agents
Knowledge
Automations
Calls
Recordings
Transcripts
Contacts
Appointments
Audit Logs
```

Asterisk's internal configuration/database should not become the application's primary database.

---

# 10. Redis

Redis is used for:

```text
Caching
Queues
Temporary State
Rate Limiting
Distributed Locks
Events
Background Jobs
Realtime Coordination
```

Redis must not be treated as permanent business data storage.

---

# 11. Object Storage

Call recordings and large media files should eventually be stored separately from PostgreSQL.

Example:

```text
Asterisk
   ↓
Recording
   ↓
Object Storage
   ↓
PostgreSQL stores metadata
```

Possible storage:

```text
S3
MinIO
S3-compatible provider
```

For local development, local filesystem storage may be used.

---

# 12. Communication Architecture

Main communication paths:

```text
Flutter
   │
   ├──── HTTPS ────→ Laravel
   │
   └──── SIP/WebRTC ────→ Asterisk

Angular
   │
   └──── HTTPS ────→ Laravel

Laravel
   │
   ├──── PostgreSQL
   ├──── Redis
   ├──── FreePBX/Asterisk
   └──── Python AI

Python
   │
   ├──── AI Provider
   ├──── Laravel
   ├──── Redis
   └──── Storage
```

---

# 13. API Boundary

The frontend must communicate through APIs.

```text
Flutter ──→ Laravel API
Angular ──→ Laravel API
```

Example:

```text
POST /api/v1/auth/login

GET /api/v1/tenants

GET /api/v1/extensions

POST /api/v1/extensions

GET /api/v1/calls

GET /api/v1/ai-agents

POST /api/v1/ai-agents
```

API versioning must be used:

```text
/api/v1/
```

Future breaking changes:

```text
/api/v2/
```

---

# 14. Internal Service Communication

Laravel and Python communicate through a controlled internal API.

```text
Laravel
   │
   │ HTTPS / Internal Network
   ▼
Python AI Service
```

Possible future architecture:

```text
Laravel
   │
   └── Queue
         │
         ▼
    AI Worker
```

The initial implementation should remain simple.

Do not introduce unnecessary microservices.

---

# 15. Telephony Architecture

The basic telephone path is:

```text
PSTN
 │
 ▼
DID Provider
 │
 ▼
SIP Trunk
 │
 ▼
FreePBX / Asterisk
 │
 ├── Extension
 ├── IVR
 ├── Queue
 ├── Ring Group
 └── AI Gateway
```

---

# 16. Incoming Call Flow

Example:

```text
Customer
   │
   │ Calls DID
   ▼
SIP Provider
   │
   ▼
Asterisk
   │
   ▼
Identify DID
   │
   ▼
Identify Tenant
   │
   ▼
Call Flow
   │
   ├───────────────┐
   │               │
   ▼               ▼
   IVR         AI Receptionist
   │               │
   │               ├── Answer
   │               ├── Transfer
   │               ├── Book
   │               └── Message
   │
   ▼
Department / Extension
```

---

# 17. DID → Tenant Resolution

The DID is a critical routing identifier.

Example:

```text
+923001111111 → Tenant A
+923002222222 → Tenant B
+923003333333 → Tenant C
```

The system must resolve:

```text
DID
 ↓
Tenant
 ↓
Business
 ↓
Call Flow
 ↓
AI Agent / Human
```

This mapping must never rely on frontend state.

---

# 18. AI Call Flow

AI calls should follow:

```text
Caller
   │
   ▼
Asterisk
   │
   ▼
AI Gateway
   │
   ▼
AI Agent Resolver
   │
   ▼
Tenant AI Configuration
   │
   ├── Instructions
   ├── Knowledge
   ├── Tools
   ├── Voice
   └── Permissions
   │
   ▼
AI Model
```

The AI Agent Resolver must identify the correct:

```text
tenant_id
agent_id
business_id
call_id
```

before starting the AI session.

---

# 19. AI Agent Architecture

Each AI agent consists of:

```text
AI Agent
│
├── Identity
├── Instructions
├── Personality
├── Knowledge
├── Tools
├── Permissions
├── Voice
├── Language
├── Business Hours
├── Transfer Rules
└── Escalation Rules
```

Example:

```text
Receptionist Agent
│
├── Knowledge
│   ├── Services
│   ├── Pricing
│   ├── Hours
│   └── FAQ
│
├── Tools
│   ├── Transfer Call
│   ├── Book Appointment
│   ├── Create Lead
│   └── Take Message
│
└── Permissions
    ├── Read Customer
    └── Create Lead
```

---

# 20. AI Tool Architecture

AI agents should NEVER directly execute arbitrary backend code.

Instead:

```text
AI
 ↓
Tool Request
 ↓
Tool Validation
 ↓
Authorization
 ↓
Tenant Validation
 ↓
Business Logic
 ↓
Result
 ↓
AI
```

Example:

```text
AI
 │
 │ book_appointment
 ▼
Laravel
 │
 ├── Verify tenant
 ├── Verify agent permissions
 ├── Validate customer
 └── Create appointment
```

---

# 21. Tenant Isolation

Every tenant-owned resource must be scoped.

Example:

```text
users
tenant_id

extensions
tenant_id

phone_numbers
tenant_id

ai_agents
tenant_id

calls
tenant_id

recordings
tenant_id
```

A request must always establish:

```text
Authenticated User
       ↓
Tenant
       ↓
Resource
```

Never:

```text
User
 ↓
Resource ID
 ↓
Return data
```

without verifying tenant ownership.

---

# 22. Tenant Isolation Rule

This is a **CRITICAL SECURITY RULE**.

A user from:

```text
Tenant A
```

must NEVER be able to access:

```text
Tenant B
```

even if they know:

```text
Tenant B resource ID
```

This must be enforced server-side.

Frontend checks are not sufficient.

---

# 23. Roles

Initial roles:

```text
Platform Owner
Platform Admin
Tenant Owner
Tenant Admin
Manager
Agent
Viewer
```

Example:

```text
Platform Admin
 └── All tenants

Tenant Admin
 └── Own tenant

Manager
 └── Assigned departments

Agent
 └── Own calls / permitted calls

Viewer
 └── Read-only permissions
```

---

# 24. Call Data Model

Every call should have a unique identifier.

```text
call_id
```

This ID should be propagated through the system.

Example:

```text
CALL-2026-000001

Asterisk
   ↓
Laravel
   ↓
Python
   ↓
Recording
   ↓
Transcript
   ↓
Automation
```

This allows complete tracing.

---

# 25. Call Lifecycle

Example:

```text
RINGING
   ↓
ANSWERED
   ↓
AI_ACTIVE
   ↓
TRANSFERRED
   ↓
HUMAN_ACTIVE
   ↓
COMPLETED
```

Possible states:

```text
initiated
ringing
answered
ai_active
human_active
on_hold
transferred
completed
missed
failed
abandoned
```

The final state model must be documented before implementation.

---

# 26. Recording Architecture

Recording:

```text
Asterisk
   ↓
Recording File
   ↓
Storage Service
   ↓
Recording Metadata
   ↓
PostgreSQL
```

Database stores:

```text
recording_id
call_id
tenant_id
storage_path
duration
format
size
created_at
```

The database should not store large audio blobs unless there is a specific reason.

---

# 27. Transcript Architecture

```text
Call
 ↓
Audio / Realtime Transcript
 ↓
Python AI
 ↓
Transcript
 ↓
PostgreSQL
```

Possible transcript structure:

```text
Speaker
Timestamp
Text
Confidence
```

Example:

```text
00:02 Customer:
I want to book an appointment.

00:05 AI:
Certainly. What day would you prefer?
```

---

# 28. AI Summary

After a call:

```text
Call
 ↓
Transcript
 ↓
AI Summary
 ↓
Call Record
```

Summary may contain:

```text
Summary
Intent
Outcome
Customer Name
Lead Status
Appointment
Follow-up
Sentiment
Action Items
```

---

# 29. IVR Architecture

The visual IVR builder must ultimately generate a representation of:

```text
Node
 ↓
Condition
 ↓
Action
 ↓
Next Node
```

Example:

```text
Incoming
   ↓
Business Hours
   ├── Open → IVR
   └── Closed → AI
```

The visual editor should NOT directly generate arbitrary Asterisk configuration.

Instead:

```text
Angular
 ↓
Laravel
 ↓
Call Flow Model
 ↓
PBX Configuration Generator
 ↓
Asterisk
```

---

# 30. Call Flow Abstraction

Use a platform-level representation.

Example:

```text
{
  "type": "business_hours",
  "conditions": {
    "open": "node_2",
    "closed": "node_5"
  }
}
```

This allows the platform to eventually support multiple telephony engines.

---

# 31. PBX Abstraction

Do not tightly couple the application database to raw FreePBX database tables.

Preferred:

```text
Application Model
       ↓
PBX Adapter
       ↓
FreePBX/Asterisk
```

Future:

```text
PBX Adapter
├── FreePBX
├── Asterisk
├── Other PBX
└── Future providers
```

---

# 32. FreePBX Integration

FreePBX/Asterisk may be controlled using supported APIs/interfaces where practical.

Potential interfaces include:

```text
FreePBX API
Asterisk ARI
Asterisk AMI
PJSIP
SIP
WebSocket
```

Use the correct interface for the job.

Do not directly manipulate internal PBX databases unless there is a documented and justified requirement.

---

# 33. ARI vs AMI

General rule:

### ARI

Use when application logic needs:

```text
Call Control
Channels
Bridges
Stasis Applications
Real-time Call Events
```

### AMI

Use for:

```text
Management
Events
Monitoring
Legacy/administrative operations
```

### PJSIP

Use for:

```text
SIP endpoints
Registration
SIP configuration
WebRTC
```

The project should document exact usage as implementation progresses.

---

# 34. Flutter SIP Architecture

Flutter should have a dedicated telephony layer.

Recommended conceptual structure:

```text
Flutter
│
├── Presentation
│
├── Application
│
├── Domain
│
└── Infrastructure
      │
      └── SIP Provider
```

Do not put SIP code directly into widgets.

---

# 35. Flutter State Management

The project should use:

```text
BLoC / Cubit
```

with a clear source of truth.

Example:

```text
SIP Engine
    ↓
Call Repository
    ↓
Call Cubit
    ↓
UI
```

The UI renders state.

The UI should not control the entire call engine.

---

# 36. Flutter Call State

Example:

```text
CallState
│
├── idle
├── registering
├── registered
├── calling
├── ringing
├── connected
├── held
├── transferring
├── ended
└── failed
```

Call state must have one authoritative source.

---

# 37. Authentication

Authentication flow:

```text
Flutter / Angular
       ↓
Laravel
       ↓
Authentication
       ↓
Token
       ↓
API
```

SIP credentials must not be treated as normal application credentials.

Laravel may provision or retrieve SIP credentials through a secure mechanism.

---

# 38. SIP Credential Security

Never expose SIP passwords unnecessarily.

Never:

```text
Commit SIP credentials
Log SIP passwords
Return credentials in unrelated API responses
Store credentials in plaintext unnecessarily
```

Where supported, use secure credential storage.

---

# 39. WebSocket Architecture

Real-time application events may use:

```text
Laravel
 ↓
WebSocket
 ↓
Angular / Flutter
```

Examples:

```text
Incoming Call
Call Status
Agent Status
Call Transfer
AI Status
Recording Ready
Automation Complete
```

Do not use polling when real-time events are clearly required.

---

# 40. Background Jobs

Long-running operations should not block normal HTTP requests.

Use queues for:

```text
AI Summary
Transcript Processing
Recording Processing
Email
Notifications
Analytics
Large Imports
Webhook Processing
```

Initial:

```text
Laravel Queue
+
Redis
```

Future:

```text
Dedicated Workers
```

---

# 41. Automation Architecture

Automation engine:

```text
Event
 ↓
Trigger
 ↓
Condition
 ↓
Action
 ↓
Result
```

Example:

```text
CALL_MISSED
      ↓
Check Customer
      ↓
Create Lead
      ↓
Notify Sales
```

Automation execution should be asynchronous where appropriate.

---

# 42. Event Architecture

Define internal events.

Examples:

```text
call.started
call.answered
call.completed
call.missed
call.transferred

ai.session.started
ai.session.completed
ai.agent.transferred

lead.created
appointment.booked

recording.created
transcript.created
summary.created
```

Events should carry:

```text
event_id
event_type
tenant_id
resource_id
timestamp
payload
```

---

# 43. Idempotency

Important operations must be idempotent.

Example:

```text
appointment.booked
```

must not create two appointments if the same event is accidentally processed twice.

Use:

```text
event_id
idempotency_key
unique constraints
```

where appropriate.

---

# 44. API Security

Every API endpoint must determine:

```text
Who?
Tenant?
Role?
Permission?
Resource?
Action?
```

Example:

```text
GET /calls/123
```

must verify:

```text
Authenticated User
        ↓
User Tenant
        ↓
Call 123 Tenant
        ↓
Permission
        ↓
Return
```

---

# 45. Rate Limiting

Rate limiting should eventually apply to:

```text
Authentication
Public APIs
AI APIs
Webhooks
Automation endpoints
File uploads
Call initiation
```

Do not allow unrestricted AI/API usage.

---

# 46. File Upload Security

Knowledge files and recordings must be validated.

Validate:

```text
File type
File size
Tenant ownership
Malware/security policy
Storage path
Access permissions
```

Never trust:

```text
Original filename
Client MIME type
Client-provided tenant ID
```

---

# 47. Knowledge Base

AI knowledge should be tenant-scoped.

```text
Tenant
  ↓
Knowledge Base
  ↓
Documents
  ↓
Chunks
  ↓
Embeddings / Search
```

Possible future architecture:

```text
PostgreSQL
+
pgvector
```

Avoid introducing a separate vector database until required.

---

# 48. AI Memory

AI memory should be separated into:

```text
Agent Configuration
Business Knowledge
Conversation Context
Customer Memory
Long-Term Memory
```

Do not mix all memory into one table.

---

# 49. AI Video Architecture

Future architecture:

```text
AI Brain
   │
   ├── STT
   ├── LLM
   ├── Tools
   ├── TTS
   │
   ▼
Avatar Engine
   │
   ▼
WebRTC
   │
   ▼
Browser / Flutter
```

The AI agent itself should remain channel-independent.

---

# 50. Provider Abstraction

External providers must be abstracted.

Examples:

```text
AIProvider
SIPProvider
StorageProvider
EmailProvider
SMSProvider
CalendarProvider
```

Example:

```text
AIProvider
├── OpenAI
├── Local Model
└── Future Provider
```

The application should not hard-code itself to one provider wherever avoidable.

---

# 51. AI Provider Strategy

Initial implementation may use a commercial AI API for reliability and speed.

Later:

```text
Commercial AI
        OR
Self-hosted AI
```

The interface should allow replacing the provider.

The project should not assume that "AI model training" is required for every agent.

Prefer:

```text
Prompt
+
Knowledge
+
Tools
+
Memory
```

before:

```text
Fine-tuning
```

---

# 52. Database Architecture

PostgreSQL is the primary application database.

Conceptual entities:

```text
tenants
businesses
users
roles
permissions

phone_numbers
extensions
departments

ivr_menus
ivr_nodes
call_flows

ai_agents
ai_agent_tools
knowledge_bases
knowledge_documents

calls
call_participants
call_recordings
call_transcripts
call_summaries

contacts
leads
appointments

automations
automation_runs

audit_logs
```

Exact schema belongs in:

```text
06-DATABASE.md
```

---

# 53. Database Rules

Never:

```text
Store business logic in JSON unnecessarily
Duplicate tenant data without reason
Use database IDs as public authorization
Skip indexes on high-volume lookup fields
```

Use indexes for common queries such as:

```text
tenant_id
phone_number
call_id
created_at
status
user_id
```

Exact indexing strategy should be determined after query design.

---

# 54. Database Migration Rules

All schema changes must use migrations.

Never manually modify production schema without documenting the change.

Migration:

```text
Code
 ↓
Migration
 ↓
Test
 ↓
Deploy
```

---

# 55. API Contract Rules

API responses must be predictable.

Use consistent:

```text
Success
Error
Validation
Pagination
Authentication
Authorization
```

Example conceptual response:

```text
{
  "success": true,
  "data": {},
  "message": "..."
}
```

Exact API format will be finalized in:

```text
07-API.md
```

---

# 56. Error Handling

Errors must be:

```text
Expected
Structured
Logged
Safe
Actionable
```

Never expose:

```text
Stack traces
Passwords
API keys
Internal infrastructure details
```

to normal users.

---

# 57. Logging

Logs must include correlation identifiers.

Prefer:

```text
tenant_id
user_id
call_id
agent_id
request_id
event_id
```

Example:

```text
[CALL-123]
tenant=ABC
agent=receptionist
event=ai.transfer
destination=101
```

Do not log sensitive information unnecessarily.

---

# 58. Monitoring

Eventually monitor:

```text
CPU
RAM
Disk
Database
Redis
Asterisk
SIP registrations
Active calls
Failed calls
AI latency
AI errors
Queue depth
Recording failures
API latency
```

---

# 59. Health Checks

Each major service should expose health status.

```text
Laravel
Python
PostgreSQL
Redis
Asterisk
Storage
```

Overall:

```text
SYSTEM HEALTH
   │
   ├── API       ✓
   ├── Database  ✓
   ├── Redis     ✓
   ├── PBX       ✓
   ├── AI        ✓
   └── Storage   ✓
```

---

# 60. Deployment Architecture — Development

Initial local architecture:

```text
Developer Machine
│
├── Docker
│
├── Laravel
├── Angular
├── Python
├── PostgreSQL
├── Redis
└── FreePBX/Asterisk
```

Flutter may run through:

```text
Physical Device
Emulator
Simulator
```

---

# 61. Deployment Architecture — MVP

Initial server:

```text
Internet
   │
   ▼
Reverse Proxy
   │
   ├── Laravel
   ├── Angular
   └── WebSocket
       
       FreePBX/Asterisk
       
       Python AI
       
       PostgreSQL
       
       Redis
       
       Object Storage
```

Exact production topology must be load-tested.

---

# 62. Scaling Architecture

When traffic increases:

```text
Load Balancer
      │
 ┌────┼────┐
 ▼    ▼    ▼
API1 API2 API3
      │
      ▼
    Redis
      │
 ┌────┴────┐
 ▼         ▼
Worker1   Worker2
      │
      ▼
 PostgreSQL
```

Asterisk scaling is a separate concern and must be designed around SIP/media/session requirements rather than simply adding web servers.

---

# 63. Scaling Principle

Scale the bottleneck.

Do not prematurely scale everything.

Potential bottlenecks:

```text
Asterisk CPU
RTP bandwidth
AI processing
AI API limits
Database
Storage
Network
WebSocket connections
```

Measure before optimizing.

---

# 64. Microservices Rule

Initial architecture is modular but NOT necessarily microservice-heavy.

Start with:

```text
Laravel
Python
Angular
Flutter
Asterisk
PostgreSQL
Redis
```

Do not create:

```text
20 microservices
```

without a real operational reason.

A service should be extracted when there is a clear benefit such as:

* Independent scaling
* Isolation
* Deployment independence
* Resource specialization
* Security boundary

---

# 65. Go

Go is optional.

It should NOT be introduced into the MVP simply because the original concept mentions Go.

Possible future Go use:

```text
High-performance SIP service
Media processing
Event gateway
Telephony worker
High-throughput service
```

Until there is a demonstrated requirement:

> Python + Laravel are sufficient.

---

# 66. Docker

Docker should provide reproducible development environments.

Expected services:

```text
postgres
redis
laravel
python-ai
angular
asterisk/freepbx
```

Each service must have:

```text
Environment configuration
Health check
Persistent storage where required
Logging
Restart policy
```

---

# 67. Environment Strategy

At minimum:

```text
development
staging
production
```

Configuration must be environment-based.

Never hard-code:

```text
API URLs
Database credentials
SIP credentials
AI keys
Storage credentials
```

---

# 68. Secrets

Secrets must come from:

```text
Environment variables
Secret manager
Secure deployment configuration
```

Never commit:

```text
.env
API keys
.pem
.p8
SIP passwords
Database passwords
```

unless the file is intentionally a non-secret example.

---

# 69. Git Architecture Rules

Branches:

```text
main
develop
feature/*
fix/*
hotfix/*
```

Suggested flow:

```text
feature
   ↓
develop
   ↓
staging
   ↓
main
```

Exact branching strategy can evolve.

---

# 70. Testing Architecture

Testing layers:

```text
Unit Tests
Integration Tests
API Tests
AI Tests
SIP Tests
Call Flow Tests
Flutter Tests
End-to-End Tests
```

Critical flows require end-to-end testing.

Example:

```text
DID
 ↓
Asterisk
 ↓
AI
 ↓
Transfer
 ↓
Flutter
 ↓
Recording
 ↓
Laravel
```

---

# 71. AI Testing

AI cannot be tested only by checking whether an HTTP request succeeded.

Test:

```text
Intent Recognition
Tool Selection
Tool Arguments
Tenant Isolation
Escalation
Transfer
Hallucination Resistance
Knowledge Accuracy
Failure Handling
```

Example:

```text
Customer asks:
"What time are you open?"

Expected:
Business hours from tenant knowledge.

Not:
A guessed answer.
```

---

# 72. AI Safety Boundary

AI must not have unrestricted authority.

Sensitive operations should require controlled tools.

Examples:

```text
Transfer Call
Book Appointment
Cancel Appointment
Create Lead
Send Message
Access Customer Data
```

Every tool should enforce authorization independently.

---

# 73. Human Escalation

AI must have a reliable human escalation path.

Example:

```text
AI
 ↓
Unable to Resolve
 ↓
Transfer Rule
 ↓
Department
 ↓
Queue / Extension
 ↓
Human
```

If the human destination is unavailable:

```text
Fallback
 ↓
Voicemail / Message / Callback
```

---

# 74. Failure Strategy

Assume every external component can fail.

Examples:

```text
AI unavailable
SIP provider unavailable
Database unavailable
Redis unavailable
Recording storage unavailable
Calendar API unavailable
```

The system should degrade gracefully.

Example:

```text
AI unavailable
      ↓
Traditional IVR
      ↓
Human extension
```

AI must never become the single point of failure for basic telephony.

---

# 75. Offline / Degraded Mode

The core PBX should continue providing basic telephony even if:

```text
AI unavailable
Laravel temporarily unavailable
External AI provider unavailable
```

This is a critical architectural principle.

---

# 76. Data Flow — Normal Call

```text
Caller
 │
 ▼
DID
 │
 ▼
SIP Provider
 │
 ▼
Asterisk
 │
 ▼
Tenant Resolver
 │
 ▼
Call Flow
 │
 ▼
AI Receptionist
 │
 ▼
AI Model
 │
 ▼
Response
 │
 ▼
Caller
```

Laravel records metadata asynchronously where possible.

---

# 77. Data Flow — AI Transfer

```text
Caller
 ↓
AI
 ↓
Intent = Human Support
 ↓
AI Tool: transfer_call
 ↓
Laravel / Telephony Controller
 ↓
Asterisk
 ↓
Extension / Queue
 ↓
Flutter Softphone
 ↓
Human
```

---

# 78. Data Flow — Missed Call Automation

```text
Incoming Call
 ↓
No Answer
 ↓
Asterisk
 ↓
call.missed
 ↓
Laravel Event
 ↓
Automation Engine
 ↓
Create Lead
 ↓
Notify Sales
```

---

# 79. Data Flow — Appointment

```text
Caller
 ↓
AI
 ↓
check_availability()
 ↓
Calendar
 ↓
AI
 ↓
book_appointment()
 ↓
Laravel
 ↓
Appointment Created
 ↓
Confirmation
```

---

# 80. Business Configuration Flow

Business owner:

```text
Angular
 ↓
Create Company
 ↓
Add DID
 ↓
Create Departments
 ↓
Create Extensions
 ↓
Create IVR
 ↓
Create AI Agent
 ↓
Upload Knowledge
 ↓
Publish
```

Backend:

```text
Laravel
 ↓
Validate
 ↓
Save PostgreSQL
 ↓
Generate PBX Configuration
 ↓
Apply Configuration
 ↓
Verify
```

---

# 81. Configuration Source of Truth

The platform's application database is the source of truth for:

```text
Business configuration
Tenant configuration
AI configuration
Automation configuration
Application users
```

Asterisk is the source of truth for:

```text
Current telephony runtime state
```

Do not allow uncontrolled configuration drift.

---

# 82. Configuration Synchronization

Conceptually:

```text
PostgreSQL
    │
    ▼
Desired Configuration
    │
    ▼
PBX Adapter
    │
    ▼
FreePBX/Asterisk
    │
    ▼
Runtime
```

Changes should be:

```text
Validated
Applied
Verified
Logged
```

---

# 83. Audit Logs

Important actions should be recorded.

Examples:

```text
User Created
Extension Created
DID Changed
AI Agent Changed
IVR Published
Recording Accessed
Call Transferred
Automation Changed
Permission Changed
```

Audit records should include:

```text
actor
tenant
action
resource
timestamp
metadata
```

---

# 84. Configuration Publishing

For complex configuration, prefer:

```text
Draft
 ↓
Validate
 ↓
Preview
 ↓
Publish
 ↓
Apply
 ↓
Verify
```

This prevents users from accidentally breaking live call routing.

---

# 85. Versioned Call Flows

Eventually call flows should be versioned.

Example:

```text
Call Flow
 ├── Version 1
 ├── Version 2
 └── Version 3 ACTIVE
```

Publishing a new version should not corrupt currently running calls.

---

# 86. Backward Compatibility

APIs and configuration should avoid unnecessary breaking changes.

Use:

```text
/api/v1
```

and explicit migrations.

AI agent configuration should also support versioning where necessary.

---

# 87. Performance Principles

Priorities:

```text
1. Reliability
2. Security
3. Correctness
4. Observability
5. Performance
6. Optimization
```

Do not sacrifice correctness for premature optimization.

---

# 88. Security Boundaries

Major security boundaries:

```text
Internet
   │
   ▼
Reverse Proxy
   │
   ▼
Application
   │
   ├── Database
   ├── Redis
   ├── AI
   └── PBX
```

PBX administration interfaces should not be unnecessarily exposed to the public Internet.

---

# 89. Network Segmentation

Where practical:

```text
PUBLIC
 │
 ├── HTTPS
 └── SIP/RTP required ports
       
PRIVATE
 │
 ├── Laravel
 ├── Python
 ├── PostgreSQL
 └── Redis
```

Internal services should not be publicly accessible unless required.

---

# 90. Public vs Private APIs

### Public

```text
Authentication
Business APIs
Mobile APIs
Admin APIs
Webhooks where required
```

### Private

```text
Internal AI APIs
Database
Redis
PBX management
Worker APIs
Internal service communication
```

---

# 91. Future Provider Independence

The application should avoid assumptions such as:

```text
Only one SIP provider
Only one AI provider
Only one storage provider
Only one calendar provider
```

Use interfaces/adapters.

Example:

```text
SIPProvider
     │
 ┌───┼────┐
 ▼   ▼    ▼
A   B    C
```

---

# 92. Recommended Initial Technology Stack

```text
Frontend:
Angular

Mobile:
Flutter

Backend:
Laravel / PHP

AI:
Python

PBX:
FreePBX + Asterisk

Database:
PostgreSQL

Cache/Queue:
Redis

Storage:
Local / S3-compatible

Infrastructure:
Docker

Realtime:
WebSocket

Telephony:
SIP / PJSIP / WebRTC
```

Go remains optional for future specialized services.

---

# 93. Architecture Decision Rule

Before introducing a new technology, answer:

```text
What problem does it solve?

Why can't the existing stack solve it?

What operational complexity does it add?

How will it be tested?

How will it be monitored?

How will it be deployed?

Can the team maintain it?
```

If these questions cannot be answered:

> Do not add the technology.

---

# 94. Non-Negotiable Architecture Rules

```text
1. Laravel is the application Control Plane.

2. Asterisk/FreePBX is the Telephony Plane.

3. Python is the AI Plane.

4. Flutter is a client.

5. Angular is a client.

6. PostgreSQL is application data source of truth.

7. Redis is not permanent business storage.

8. AI never gets unrestricted database access.

9. Tenant isolation is enforced server-side.

10. Frontend is never trusted for authorization.

11. Secrets never go into source control.

12. Core telephony must not depend entirely on AI.

13. Avoid unnecessary microservices.

14. Avoid premature Kubernetes.

15. Avoid premature Go adoption.

16. External providers must be replaceable where practical.

17. All important operations must be observable.

18. Complex configuration must be validated before publishing.

19. Existing functionality must be inspected before creating replacements.

20. Architectural changes require documentation.
```

---

# 95. Definition of Done — Architecture

Architecture is considered implemented only when:

```text
✓ Component boundaries defined
✓ Communication paths defined
✓ Tenant model defined
✓ Authentication boundary defined
✓ Authorization boundary defined
✓ Telephony boundary defined
✓ AI boundary defined
✓ Database responsibility defined
✓ Recording flow defined
✓ Call lifecycle defined
✓ Error strategy defined
✓ Scaling strategy defined
✓ Security boundaries defined
✓ Testing strategy defined
✓ Documentation updated
```

---

# 96. Current Architecture Phase

Current architecture should remain intentionally simple:

```text
                 INTERNET
                    │
                    ▼
              SIP / HTTPS
                    │
        ┌───────────┴───────────┐
        ▼                       ▼
     Asterisk                Laravel
        │                       │
        │                    PostgreSQL
        │                       │
        │                     Redis
        │                       │
        │                    Python
        │                       │
        ▼                       ▼
    Flutter                 AI Service
        │
        ▼
    Employees
```

Do not prematurely distribute every component across multiple servers.

---

# 97. Evolution Path

### Stage 1

```text
Single deployment
```

### Stage 2

```text
Separate AI workers
Separate application workers
```

### Stage 3

```text
Multiple application servers
Dedicated PBX infrastructure
Object storage
```

### Stage 4

```text
Multiple PBX nodes
AI worker cluster
Database scaling
```

### Stage 5

```text
Multi-region
High availability
Advanced routing
```

Architecture evolves based on real requirements.

---

# 98. Final Architecture Principle

The architecture must maintain this separation:

```text
                    ┌───────────────┐
                    │    Flutter    │
                    │    Angular    │
                    └───────┬───────┘
                            │
                            ▼
                    ┌───────────────┐
                    │    Laravel    │
                    │ CONTROL PLANE │
                    └───┬───────┬───┘
                        │       │
              ┌─────────┘       └─────────┐
              ▼                           ▼
       ┌─────────────┐             ┌─────────────┐
       │ FreePBX /   │             │   Python    │
       │  Asterisk   │             │     AI      │
       │ TELEPHONY   │             │   AI PLANE  │
       └─────────────┘             └─────────────┘
              │                           │
              └─────────────┬─────────────┘
                            ▼
                    ┌───────────────┐
                    │ PostgreSQL /  │
                    │ Redis / Store │
                    └───────────────┘
```

The platform should remain:

> **Simple at the top, powerful underneath.**

Business users see:

```text
Business
 ↓
Phone Number
 ↓
AI Agent
 ↓
Call Flow
 ↓
Automation
```

The infrastructure handles:

```text
SIP
PJSIP
RTP
Asterisk
AI
APIs
Queues
Databases
Storage
Workers
```

The complexity exists — but the user does not need to see it.
