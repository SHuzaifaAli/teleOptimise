# 03 — Prerequisites

## 1. Purpose

This document defines everything required before development of the AI-PBX Platform begins.

The goal is to prevent the team or AI coding agents from starting implementation while critical infrastructure, accounts, credentials, network access, or architecture decisions are still missing.

---

# 2. Prerequisite Categories

We divide prerequisites into four levels:

| Level          | Meaning                              |
| -------------- | ------------------------------------ |
| 🔴 Required    | Must be available before development |
| 🟡 Recommended | Strongly recommended for MVP         |
| 🟢 Optional    | Can be added later                   |
| ⚪ Future       | Not required for MVP                 |

---

# 3. Target MVP Stack

The initial MVP will use:

```text
Flutter
    ↓
SIP / WebRTC
    ↓
FreePBX
    ↓
Asterisk
    ↓
SIP Trunk / DID
```

Administration:

```text
Angular
    ↓
Laravel API
    ↓
PostgreSQL
```

AI:

```text
Python AI Service
    ↓
AI Model / Realtime Voice
    ↓
Tools
    ↓
Laravel APIs
    ↓
Asterisk
```

Infrastructure:

```text
PostgreSQL
Redis
Object Storage
Docker
Linux Server
HTTPS
```

---

# 4. Development Machine

## Required

Development should be possible from:

* macOS
* Linux
* Windows

Recommended:

```text
CPU:      4+ cores
RAM:      16 GB+
Storage:  100 GB+ free
Internet: Stable broadband
```

For heavy local PBX + AI development:

```text
RAM:      32 GB preferred
CPU:      6–8 cores preferred
Storage:  150–250 GB preferred
```

A developer does NOT need to run the entire production infrastructure locally.

---

# 5. Operating System

## Recommended

For server-side development:

```text
Ubuntu 24.04 LTS
```

Alternative:

```text
Ubuntu 22.04 LTS
Debian
```

The production PBX should preferably run on a dedicated Linux server/VM.

---

# 6. Git

## Required

Install:

```text
Git
```

Verify:

```bash
git --version
```

Configure:

```bash
git config --global user.name "Your Name"
git config --global user.email "your@email.com"
```

---

# 7. GitHub

## Required

A GitHub organization/repository should be created.

Recommended structure:

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

The exact repository structure can be changed later.

---

# 8. Docker

## Required

Install:

```text
Docker
Docker Compose
```

Verify:

```bash
docker --version
docker compose version
```

Docker will be used for local development of:

* Laravel
* PostgreSQL
* Redis
* Python AI service
* Angular
* supporting services

---

# 9. PHP / Laravel

## Required

Laravel backend requires a supported PHP version.

The exact PHP/Laravel version must be pinned in the repository before Sprint 1.

Recommended:

```text
PHP 8.3+
Composer 2.x
Laravel current supported LTS/stable release
```

Verify:

```bash
php -v
composer --version
```

Laravel responsibilities:

```text
Authentication
Authorization
Multi-tenancy
Business management
Users
Extensions
DIDs
Departments
IVR configuration
AI configuration
Automations
Call metadata
Recording metadata
Audit logs
API
Webhooks
```

Laravel must NOT become the SIP/media engine.

---

# 10. Python

## Required

Python:

```text
Python 3.11+
```

Verify:

```bash
python3 --version
```

Recommended:

```text
Python 3.11 or newer
```

Python will handle:

* AI agents
* AI orchestration
* realtime voice processing
* speech processing
* knowledge retrieval
* tool calling
* call summaries
* transcription
* AI analytics
* future AI video

Use a virtual environment:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

---

# 11. Node.js

## Required

Angular requires Node.js.

Recommended:

```text
Node.js LTS
npm
```

Verify:

```bash
node --version
npm --version
```

Install Angular CLI when required:

```bash
npm install -g @angular/cli
```

---

# 12. Flutter

## Required

Flutter SDK:

```text
Stable channel
```

Verify:

```bash
flutter doctor
```

The Flutter application will act as a SIP/WebRTC softphone.

It must support:

* login
* tenant/user context
* SIP registration
* dial pad
* outgoing calls
* incoming calls
* call screen
* mute
* speaker
* hold
* resume
* transfer
* conference
* call history
* voicemail
* presence

---

# 13. Flutter SIP Requirements

The SIP layer must be selected before implementing the production calling layer.

Potential approaches:

```text
SIP over WebSocket
SIP over UDP/TCP
WebRTC
Native SIP library
Flutter SIP package
```

For a modern mobile softphone:

```text
Flutter
    ↓
SIP/WebRTC abstraction
    ↓
PJSIP/Asterisk-compatible infrastructure
```

The application architecture should isolate the SIP implementation behind an interface.

Example:

```text
SipService
    ↓
SipRepository
    ↓
CallBloc
    ↓
CallScreen
```

Do not tightly couple UI code to the SIP library.

---

# 14. FreePBX

## Required

FreePBX will provide the administrative PBX layer.

It will sit on top of:

```text
Asterisk
```

Required functionality:

* extensions
* trunks
* inbound routes
* outbound routes
* IVR
* ring groups
* queues
* voicemail
* conference
* recording
* dialplan
* PJSIP
* WebRTC

FreePBX is not the AI engine.

---

# 15. Asterisk

## Required

Asterisk is the actual telephony engine.

The platform will use Asterisk for:

```text
SIP
PJSIP
RTP
Call routing
Dialplan
Media
IVR
Queues
Recording
Transfers
Conferences
WebRTC
Call events
```

Important interfaces:

```text
PJSIP
ARI
AMI
Dialplan
```

General rule:

```text
PJSIP → SIP endpoints
ARI    → application call control
AMI    → management/events
Dialplan → deterministic telephony logic
```

Do not use AI where deterministic PBX logic is sufficient.

---

# 16. SIP Trunk

## 🔴 Required for Real Phone Calls

A SIP trunk connects the PBX to the public telephone network.

Without a SIP trunk:

```text
Flutter → Asterisk
```

can work internally.

But:

```text
Real Phone
    ↓
PSTN
    ↓
SIP Provider
    ↓
Asterisk
```

requires a provider.

Required provider capabilities:

* inbound calls
* outbound calls
* DID
* SIP credentials or IP authentication
* compatible codecs
* RTP
* DTMF
* international calling if required

---

# 17. DID

## Required for External Incoming Calls

DID means:

> Direct Inward Dialing

A DID is a real telephone number that routes incoming calls to your SIP infrastructure.

Example:

```text
+92 XXX XXXXXXX
        ↓
SIP Provider
        ↓
Asterisk
        ↓
Tenant
        ↓
IVR / AI / Extension
```

Each company can have one or multiple DIDs.

Example:

```text
Company A
+1 555 100 1000

Company B
+1 555 100 2000

Company C
+1 555 100 3000
```

The backend maps:

```text
DID → Tenant → Business → Call Flow
```

This mapping must be server-side.

---

# 18. Important Cost Warning

Open-source software can make the platform inexpensive to build, but the complete communication system is NOT necessarily free.

Possible paid services:

```text
DID
SIP trunk
PSTN minutes
SMS
AI API
Cloud server
Bandwidth
Object storage
Email
Calendar integrations
```

Therefore:

```text
Open Source ≠ Zero Operating Cost
```

The MVP should minimize recurring costs but must not assume external telephony or AI services are free.

---

# 19. AI Provider

## Required for AI Receptionist

The AI system needs a model provider or self-hosted model.

The reference AI receptionist project currently expects an OpenAI API key with Realtime API access.

Use a standard API key.

Example:

```text
OPENAI_API_KEY=...
```

Never commit API keys to Git.

Use:

```text
.env
Docker secrets
Secret manager
Environment variables
```

depending on deployment stage.

---

# 20. AI Voice Architecture Decision

Before Sprint 1, choose one primary AI voice architecture.

### Option A — LiveKit + AI

```text
SIP
 ↓
LiveKit SIP
 ↓
AI Agent
 ↓
Realtime AI
```

Advantages:

* strong realtime architecture
* WebRTC-friendly
* scalable
* good separation of media and AI
* easier future AI/video expansion

Disadvantages:

* additional infrastructure
* another component to operate
* SIP/media networking becomes more complex

---

### Option B — Direct Asterisk + AI

```text
SIP
 ↓
Asterisk
 ↓
ARI / Media
 ↓
Python AI
```

Advantages:

* fewer major components
* Asterisk remains central
* potentially simpler infrastructure

Disadvantages:

* realtime media integration is harder
* more custom engineering
* AI audio streaming becomes our responsibility

---

### MVP Recommendation

Start with:

```text
FreePBX/Asterisk
        +
Python AI
```

while keeping a clean abstraction so LiveKit can be introduced where it provides real value.

Do NOT introduce both architectures everywhere without a reason.

---

# 21. PostgreSQL

## Required

PostgreSQL is the application source of truth.

It stores:

```text
Tenants
Businesses
Users
Roles
Extensions
DIDs
Departments
IVRs
Call flows
Calls
Recordings
Transcripts
AI Agents
Knowledge
Automations
Audit Logs
```

PostgreSQL should NOT store large audio/video files.

---

# 22. Redis

## Recommended for MVP

Redis will be used for:

```text
Caching
Queues
Temporary state
Locks
Rate limiting
Realtime coordination
Background jobs
```

Do not use Redis as permanent business storage.

---

# 23. Object Storage

## Required before Production Recordings

Recordings should be stored separately from PostgreSQL.

Possible storage:

```text
S3-compatible storage
MinIO
Cloud object storage
```

Development:

```text
Local storage / MinIO
```

Production:

```text
S3-compatible object storage
```

Store metadata in PostgreSQL:

```text
recording_id
call_id
tenant_id
file_path
duration
format
size
created_at
```

---

# 24. Domain

## Required for Production

The production system needs a domain.

Example:

```text
example.com
```

Recommended subdomains:

```text
api.example.com
admin.example.com
ai.example.com
pbx.example.com
```

Exact naming can change.

---

# 25. SSL/TLS

## Required

Production communication must use HTTPS/TLS.

Required areas:

```text
Angular → Laravel
Flutter → Laravel
Flutter → SIP/WebSocket
AI → internal services
PBX → SIP/WebRTC where applicable
```

Never run production authentication or SIP signaling over insecure connections when encrypted alternatives are supported.

---

# 26. Firewall

The production architecture must define:

```text
Allowed ports
Source IPs
VPN access
SIP access
RTP ranges
HTTPS
SSH
Database access
Redis access
```

Never expose:

```text
PostgreSQL
Redis
internal admin APIs
```

directly to the public Internet unless there is a deliberate secured architecture.

---

# 27. SIP / RTP Networking

SIP is not just HTTP.

The infrastructure must account for:

```text
SIP signaling
RTP media
NAT
Firewall
STUN
TURN
TLS
WebSocket
WebRTC
```

For WebRTC, expect requirements around:

```text
ICE
STUN
TURN
DTLS
SRTP
```

Poor NAT configuration can cause:

```text
One-way audio
No audio
Failed calls
Failed registration
```

Therefore telephony networking must be tested independently from the Flutter UI.

---

# 28. DNS

Configure DNS for:

```text
Main domain
API
Admin portal
PBX
AI service
```

Example:

```text
api.example.com
admin.example.com
pbx.example.com
```

DNS should point to the correct public infrastructure.

---

# 29. Email

## Optional for MVP

Needed for:

```text
Password reset
User invitations
Notifications
Reports
Alerts
```

Possible providers:

```text
SMTP
Resend
Postmark
Amazon SES
Self-hosted SMTP
```

Provider selection should be abstracted.

---

# 30. SMS

## Optional

Useful for:

```text
Appointment reminders
OTP
Notifications
Missed-call follow-up
AI automation
```

Do not make SMS a core dependency of the MVP.

---

# 31. Calendar Integration

## Optional

Future integrations:

```text
Google Calendar
Microsoft Outlook
Other calendar providers
```

This becomes important for AI receptionist capabilities such as:

```text
"Can I book an appointment?"
"Is Tuesday available?"
"Move my appointment to Friday."
```

---

# 32. CRM Integration

## Future

Potential integrations:

```text
HubSpot
Salesforce
Zoho
Custom CRM
```

The AI agent should eventually interact with CRMs through tools.

Example:

```text
AI
 ↓
get_customer()
 ↓
CRM
```

AI should not receive unrestricted CRM database access.

---

# 33. Recording & Compliance

## Required before production

Before enabling recording, determine:

* recording laws
* consent requirements
* retention policy
* deletion policy
* access permissions
* tenant isolation
* encryption
* transcript retention
* AI processing consent

The exact requirements depend on the countries/states where calls occur.

The platform should support configurable policies rather than assuming one global rule.

---

# 34. AI Privacy

AI processing can involve:

```text
voice
transcripts
customer information
phone numbers
names
appointments
business information
```

Therefore:

```text
AI → minimum required information
AI → authorized tools only
AI → tenant-scoped data
```

Never provide an AI agent with:

```text
SELECT * FROM database
```

or unrestricted database credentials.

---

# 35. Authentication

Laravel should provide centralized authentication.

Minimum requirements:

```text
Login
Logout
Password reset
Token/session management
Role-based access
Tenant authorization
```

Future:

```text
2FA
SSO
OAuth
Enterprise authentication
```

---

# 36. Roles

Minimum roles:

```text
Platform Admin
Tenant Admin
Business Admin
Manager
Agent
User
```

The exact role model may evolve.

Authorization must be checked server-side.

---

# 37. Multi-Tenant Requirements

The system must be multi-tenant from the beginning.

Every tenant-owned resource should have:

```text
tenant_id
```

Example:

```text
users
businesses
extensions
phone_numbers
departments
ivr_flows
calls
recordings
ai_agents
automations
```

Never rely only on frontend filtering.

Correct:

```text
Request
 ↓
Authenticated User
 ↓
Tenant
 ↓
Authorization
 ↓
Resource
```

Incorrect:

```text
Frontend sends tenant_id
 ↓
Backend trusts it
```

---

# 38. Environment Configuration

At minimum:

```text
development
staging
production
```

Example:

```text
.env.development
.env.staging
.env.production
```

Never commit real credentials.

Use placeholders:

```text
DATABASE_URL=
REDIS_URL=
OPENAI_API_KEY=
SIP_USERNAME=
SIP_PASSWORD=
S3_ACCESS_KEY=
S3_SECRET_KEY=
```

---

# 39. Local Development Architecture

Recommended local environment:

```text
┌───────────────────────────┐
│ Flutter                   │
└─────────────┬─────────────┘
              │
              ↓
┌───────────────────────────┐
│ Laravel                   │
└─────────────┬─────────────┘
              │
       ┌──────┴──────┐
       ↓             ↓
 PostgreSQL        Redis
       
┌───────────────────────────┐
│ Python AI Service         │
└───────────────────────────┘

┌───────────────────────────┐
│ FreePBX / Asterisk        │
└───────────────────────────┘

┌───────────────────────────┐
│ Angular Admin             │
└───────────────────────────┘
```

Not every service must run directly on the developer's machine.

---

# 40. Recommended Development Strategy

Do NOT immediately build:

```text
20 microservices
Kubernetes
Multi-region
AI video
Marketplace
Billing
Advanced CRM
```

Start with a small number of services:

```text
Laravel
Python AI
Angular
Flutter
FreePBX/Asterisk
PostgreSQL
Redis
Object Storage
```

Then split services only when there is a measurable reason.

---

# 41. Required Documentation Tools

Recommended:

```text
Markdown
Mermaid
OpenAPI
Postman/Insomnia
Swagger
GitHub Issues
GitHub Projects
```

Architecture diagrams should preferably be written using Mermaid so AI agents can understand and modify them.

---

# 42. API Documentation

Laravel APIs should eventually have an OpenAPI specification.

Example:

```text
/api/v1/auth/login
/api/v1/tenants
/api/v1/businesses
/api/v1/users
/api/v1/extensions
/api/v1/phone-numbers
/api/v1/ivrs
/api/v1/calls
/api/v1/recordings
/api/v1/ai-agents
/api/v1/automations
```

The API version must be explicit.

---

# 43. Testing Prerequisites

Required testing layers:

```text
Unit Tests
Integration Tests
API Tests
Flutter Tests
SIP Tests
PBX Tests
AI Tool Tests
End-to-End Tests
```

Critical call scenarios:

```text
Inbound call
Outbound call
Missed call
Transfer
Hold
Resume
Conference
IVR
AI receptionist
AI → human transfer
Recording
Call termination
Network failure
PBX failure
AI failure
```

---

# 44. Monitoring

## Required before Production

Monitor at least:

```text
API uptime
PBX uptime
SIP registration
Active calls
Failed calls
Call duration
AI latency
AI failures
CPU
RAM
Disk
Database
Redis
Storage
```

Future:

```text
Prometheus
Grafana
OpenTelemetry
Sentry
ELK/OpenSearch
```

---

# 45. Backup

Production must have backups for:

```text
PostgreSQL
Configuration
Tenant data
Recordings
AI configuration
PBX configuration
```

Define:

```text
Backup frequency
Retention
Encryption
Restore process
Disaster recovery
```

A backup is not considered complete until restoration has been tested.

---

# 46. Security Prerequisites

Minimum:

```text
HTTPS
Strong passwords
Password hashing
JWT/session security
RBAC
Tenant isolation
Rate limiting
Input validation
SQL injection protection
File validation
Audit logging
Secret management
Firewall
Database access restrictions
```

Never commit:

```text
API keys
Passwords
SIP credentials
Private keys
Certificates
Production .env files
```

---

# 47. Reference Repository License

The reference AI receptionist repository must be reviewed before copying code.

Important:

```text
AIReceptionist
AGPL-3.0
```

Do not copy code blindly into a proprietary product.

Before reusing code:

```text
Identify license
Review obligations
Check dependencies
Record attribution requirements
Decide whether implementation should be rewritten
```

Use open-source projects as architectural references when appropriate, but do not assume that all code can be copied into the final commercial product.

---

# 48. LiveKit License

If LiveKit components are introduced, verify the specific component and repository license before distribution.

Do not assume:

```text
"Open Source" = "No license obligations"
```

Every dependency should be recorded.

---

# 49. Dependency Management

Maintain:

```text
Backend dependencies
Python dependencies
Angular dependencies
Flutter dependencies
Asterisk modules
Docker images
Infrastructure dependencies
```

Recommended files:

```text
composer.json
requirements.txt / pyproject.toml
package.json
pubspec.yaml
Dockerfile
docker-compose.yml
```

Pin production-critical versions.

---

# 50. Accounts Required

Minimum accounts:

### Development

* GitHub
* AI provider account
* Docker environment

### Telephony

* SIP provider
* DID provider

### Production

* Domain registrar
* DNS provider
* VPS/cloud provider
* Object storage provider

### Optional

* Email provider
* SMS provider
* Calendar provider
* CRM provider
* Monitoring provider

---

# 51. Information Required From Client

Before production deployment, collect:

## Company Information

```text
Company name
Business name
Industry
Country
Timezone
Business hours
Holiday schedule
```

## Telephony

```text
Existing phone numbers
DIDs
SIP provider
SIP credentials
Existing PBX
Existing extensions
Required extensions
Required departments
```

## Call Flow

```text
Greeting
IVR
Departments
Business hours
After-hours behavior
Holiday behavior
Queue behavior
Voicemail
Transfer rules
Escalation rules
```

## AI Receptionist

```text
Agent name
Voice
Language
Personality
Greeting
Business knowledge
FAQs
Allowed actions
Transfer destinations
Escalation rules
Working hours
```

## Recording

```text
Should calls be recorded?
Retention duration
Who can access recordings?
Transcript required?
AI analysis required?
```

---

# 52. AI Agent Information

Each AI agent should eventually have:

```text
Agent ID
Tenant ID
Business ID
Name
System instructions
Personality
Voice
Language
Knowledge sources
Tools
Permissions
Working hours
Transfer rules
Escalation rules
Fallback behavior
```

Example:

```text
Agent:
Sarah

Role:
Receptionist

Personality:
Professional + friendly

Can:
- Answer FAQs
- Check appointments
- Book appointments
- Transfer calls
- Take messages

Cannot:
- Access unrelated tenants
- Delete customers
- Modify billing
- Execute arbitrary database queries
```

---

# 53. Knowledge Base Prerequisites

AI receptionist requires business knowledge.

Possible sources:

```text
FAQs
PDFs
Websites
Documents
Policies
Product catalogs
Service information
Pricing
Opening hours
Location information
```

Future pipeline:

```text
Document
 ↓
Parse
 ↓
Chunk
 ↓
Embed
 ↓
Vector Store
 ↓
Retrieve
 ↓
AI Agent
```

Knowledge ingestion should be tenant-scoped.

---

# 54. Automation Prerequisites

The automation engine will eventually use:

```text
Trigger
 ↓
Conditions
 ↓
Actions
```

Example:

```text
Trigger:
Missed call

Condition:
Caller is existing customer

Action:
Send SMS
```

Possible triggers:

```text
Incoming call
Missed call
Call completed
AI escalation
New lead
Appointment created
Appointment cancelled
Recording completed
Payment event
Webhook
Schedule
```

---

# 55. Future AI Video

AI video is NOT an MVP prerequisite.

It should be treated as:

```text
Phase 2 / Phase 3
```

Future architecture may include:

```text
Video Call
 ↓
WebRTC
 ↓
AI Avatar
 ↓
Speech
 ↓
LLM
 ↓
Vision
 ↓
Tools
```

Do not allow AI video requirements to block the telephone MVP.

---

# 56. Go

Go is currently optional.

Although Go may be useful for:

```text
High-performance services
Realtime event processing
SIP-related infrastructure
Media services
High-concurrency workers
```

it should NOT be introduced into the MVP without a specific requirement.

Current recommendation:

```text
Laravel → application/control plane
Python → AI
Asterisk → telephony
Flutter → mobile
Angular → admin
```

Add Go only when justified.

---

# 57. Definition of Ready

A development task is ready when:

```text
[ ] Requirement is understood
[ ] API contract is defined
[ ] Database impact is known
[ ] Tenant ownership is known
[ ] Authorization rules are known
[ ] Error states are defined
[ ] Tests are identified
[ ] Dependencies are available
[ ] No critical prerequisite is missing
```

---

# 58. Sprint 1 Gate

Do not start serious implementation until the following are available:

```text
[ ] Git repository
[ ] Development machine
[ ] Docker
[ ] Flutter
[ ] Node.js
[ ] PHP
[ ] Composer
[ ] Python 3.11+
[ ] PostgreSQL
[ ] Redis
[ ] FreePBX/Asterisk development environment
[ ] Basic SIP knowledge
[ ] Architecture document
[ ] Repository structure
[ ] Environment configuration strategy
```

---

# 59. Before Real Phone Testing

The following must be available:

```text
[ ] SIP provider
[ ] DID
[ ] SIP credentials
[ ] Public server/IP
[ ] DNS
[ ] Firewall configuration
[ ] RTP configuration
[ ] TLS certificates
[ ] Test phone
[ ] Flutter softphone build
```

---

# 60. Before AI Call Testing

Required:

```text
[ ] AI provider/API
[ ] AI API key
[ ] Python AI service
[ ] SIP call working
[ ] Audio path working
[ ] AI agent configuration
[ ] Basic business knowledge
[ ] AI tool interface
[ ] Transfer destination
[ ] Failure fallback
```

The correct sequence is:

```text
SIP works
   ↓
Audio works
   ↓
Call routing works
   ↓
AI connects
   ↓
AI talks
   ↓
AI uses tools
   ↓
AI transfers
```

Do not debug all layers simultaneously.

---

# 61. Before Multi-Tenant Testing

Required:

```text
[ ] Tenant table
[ ] Tenant authentication
[ ] Tenant authorization
[ ] tenant_id strategy
[ ] Tenant-scoped queries
[ ] Tenant-scoped files
[ ] Tenant-scoped AI agents
[ ] Tenant-scoped DIDs
[ ] Tenant-scoped recordings
[ ] Cross-tenant security tests
```

Test:

```text
Tenant A cannot access Tenant B.
```

This must be tested at the API level.

---

# 62. Before Production

Final checklist:

```text
INFRASTRUCTURE
[ ] Production server
[ ] Domain
[ ] DNS
[ ] SSL
[ ] Firewall
[ ] Monitoring
[ ] Backups

TELEPHONY
[ ] SIP trunk
[ ] DID
[ ] Inbound calling
[ ] Outbound calling
[ ] RTP
[ ] DTMF
[ ] IVR
[ ] Transfers
[ ] Recording

APPLICATION
[ ] Laravel
[ ] Angular
[ ] Flutter
[ ] PostgreSQL
[ ] Redis
[ ] Object storage

AI
[ ] AI provider
[ ] AI agent
[ ] Knowledge base
[ ] Tool calling
[ ] Transfer
[ ] Fallback
[ ] Usage monitoring

SECURITY
[ ] RBAC
[ ] Tenant isolation
[ ] Secrets
[ ] Audit logs
[ ] Rate limits
[ ] Backup restore test

COMPLIANCE
[ ] Recording policy
[ ] AI disclosure/consent
[ ] Data retention
[ ] Privacy policy
[ ] Terms
```

---

# 63. Recommended Initial Infrastructure

For MVP:

```text
                    INTERNET
                       │
                       ▼
                ┌─────────────┐
                │ SIP Provider│
                └──────┬──────┘
                       │
                       ▼
                ┌─────────────┐
                │ FreePBX     │
                │ Asterisk    │
                └──────┬──────┘
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
       Flutter Softphone     Python AI
                                   │
                                   ▼
                              AI Provider

                       APPLICATION
                            │
                            ▼
                        Laravel API
                       /           \
                      ▼             ▼
                 PostgreSQL       Redis
                      
                            │
                            ▼
                       Object Storage

                            ▲
                            │
                     Angular Admin
```

This is intentionally simple.

---

# 64. What We Should NOT Require Initially

Do not block development waiting for:

```text
[ ] Kubernetes
[ ] Multi-region deployment
[ ] AI video
[ ] Advanced CRM
[ ] Marketplace
[ ] Billing platform
[ ] WhatsApp ecosystem
[ ] Enterprise SSO
[ ] Advanced fine-tuning
[ ] Custom AI model training
[ ] Massive-scale infrastructure
```

These belong to later phases.

---

# 65. Critical Technical Principle

The platform must be built in this order:

```text
1. Infrastructure
       ↓
2. Asterisk / FreePBX
       ↓
3. SIP
       ↓
4. Flutter Softphone
       ↓
5. Laravel
       ↓
6. Angular
       ↓
7. Multi-Tenancy
       ↓
8. IVR
       ↓
9. Recording
       ↓
10. AI Receptionist
       ↓
11. AI Tools
       ↓
12. Automations
       ↓
13. Analytics
       ↓
14. Scaling
       ↓
15. AI Video
```

---

# 66. Golden Rule

Never build the AI layer on top of an unreliable telephony layer.

Correct:

```text
Reliable SIP
    ↓
Reliable Calls
    ↓
Reliable Routing
    ↓
Reliable Recording
    ↓
AI
```

Incorrect:

```text
AI first
 ↓
Broken SIP
 ↓
Broken audio
 ↓
Debug everything simultaneously
```

---

# 67. Final Prerequisite Status

Before Sprint 1:

### 🔴 Must Have

```text
Git
Docker
Flutter
PHP/Laravel
Python
Node/Angular
PostgreSQL
FreePBX/Asterisk
Basic SIP environment
Architecture
Repository
Environment strategy
```

### 🟡 Strongly Recommended

```text
Redis
MinIO/S3
Domain
HTTPS
Staging server
Monitoring
```

### 🟢 Optional

```text
SMS
Email
Calendar
CRM
LiveKit
```

### ⚪ Later

```text
Go
AI Video
Kubernetes
Multi-region
Advanced AI training
Marketplace
Billing
```

---

# 68. Prerequisite Completion Rule

The project can move to Sprint 1 when:

```text
Required prerequisites = 100%
```

The project does NOT need:

```text
Optional prerequisites = 100%
```

If a missing prerequisite blocks a specific feature, mark that feature:

```text
BLOCKED
```

instead of implementing a fake workaround.

Example:

```text
DID unavailable
    ↓
Internal SIP calls can still be developed
    ↓
PSTN testing = BLOCKED
```

This keeps development moving without hiding infrastructure problems.

---

# 69. Final Principle

> **Build the communication foundation first. Make it intelligent second. Make it autonomous third.**

The platform should become a reliable PBX before attempting to become an AI employee.
