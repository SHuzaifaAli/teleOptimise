# 05 — Vibe Coding Rules

## 1. Purpose

This document defines how AI coding agents and developers must work on the AI-PBX Platform.

The project will use AI heavily for:

* architecture assistance
* code generation
* debugging
* refactoring
* documentation
* testing
* code review
* infrastructure configuration
* feature implementation

However:

> **AI is an implementation assistant, not the project architect or product owner.**

AI must follow the project's existing architecture, rules, contracts, and roadmap.

---

# 2. Golden Rule

```text
READ → UNDERSTAND → PLAN → IMPLEMENT → TEST → REVIEW → COMMIT
```

Never:

```text
PROMPT → GENERATE EVERYTHING → HOPE IT WORKS
```

---

# 3. Mandatory Reading Order

Before modifying code, every AI coding agent MUST read:

```text
01. 00-README.md
02. 01-PROJECT-VISION.md
03. 02-ARCHITECTURE.md
04. 03-PREREQUISITES.md
05. 04-SPRINT-ROADMAP.md
06. 05-VIBE-CODING-RULES.md
```

Then inspect the relevant source code.

Do not start implementation before understanding the existing architecture.

---

# 4. Current Sprint First

Every task must identify:

```text
Sprint
Feature
Task
Subtask
```

Example:

```text
Sprint: S04
Feature: Flutter SIP
Task: SIP Registration
Subtask: Registration state handling
```

If the task does not belong to the current roadmap:

```text
STOP
```

and ask whether it should be added to the roadmap.

---

# 5. One Primary Task at a Time

AI agents should avoid implementing multiple unrelated features in one task.

Bad:

```text
Build SIP
Build authentication
Build IVR
Build AI
Build dashboard
Fix Docker
```

Good:

```text
Implement SIP registration.
```

Then:

```text
Test SIP registration.
```

Then:

```text
Implement incoming calls.
```

This makes debugging dramatically easier.

---

# 6. Never Guess Requirements

If something is unknown:

```text
UNKNOWN
```

is better than inventing an answer.

Example:

```text
Unknown:
Which SIP provider will be used?
```

Do not invent:

```text
Twilio
```

unless the project has explicitly selected Twilio.

---

# 7. Never Fake Infrastructure

If infrastructure is unavailable, do not create fake production behavior.

Example:

```text
DID unavailable
```

Allowed:

```text
Continue extension-to-extension testing.
```

Not allowed:

```text
Hardcode a fake DID and claim PSTN calling works.
```

Use mocks only when the task explicitly requires mocking.

---

# 8. Never Hide Blockers

If a task cannot be completed, report:

```text
BLOCKED

Reason:
SIP credentials are unavailable.

Required:
SIP username/password or IP authentication details.
```

Do not silently work around critical infrastructure problems.

---

# 9. Inspect Before Editing

Before changing a file:

```text
1. Locate the file.
2. Read the relevant code.
3. Understand dependencies.
4. Identify callers.
5. Identify tests.
6. Make the smallest safe change.
```

Never blindly replace an entire file.

---

# 10. Minimal Change Principle

Prefer:

```text
Small change
```

over:

```text
Large rewrite
```

unless a rewrite is explicitly required.

Example:

If one API parser is broken:

```text
Fix parser.
```

Do not:

```text
Rewrite entire repository architecture.
```

---

# 11. No Unrequested Refactoring

Do not modify unrelated code.

If working on:

```text
SIP registration
```

do not suddenly refactor:

```text
authentication
database
UI
AI service
```

unless the dependency is real and documented.

---

# 12. Architecture Is a Contract

The architecture defined in:

```text
02-ARCHITECTURE.md
```

must be treated as a contract.

Current conceptual structure:

```text
Flutter
   ↓
Laravel
   ↓
PostgreSQL / Redis

Angular
   ↓
Laravel

Python AI
   ↓
Laravel / Telephony

FreePBX
   ↓
Asterisk
```

Do not introduce alternative architecture casually.

---

# 13. Layer Responsibilities

## Flutter

Flutter owns:

```text
UI
User interaction
Local presentation state
SIP client interaction
Local secure storage
Client-side validation
```

Flutter does NOT own:

```text
Tenant authorization
AI business logic
PBX configuration
Database access
Server secrets
Business rules
```

---

# 14. Laravel

Laravel owns:

```text
Authentication
Authorization
Tenants
Users
Businesses
Application data
Business rules
API
Webhooks
PBX orchestration
AI configuration
Automation configuration
Audit logs
```

Laravel does NOT become:

```text
SIP media engine
AI model
Flutter UI
```

---

# 15. Python AI

Python owns:

```text
AI agents
AI orchestration
Realtime AI
Speech processing
Knowledge retrieval
Tool execution orchestration
Transcription
Summaries
AI analytics
```

Python must NOT bypass authorization.

---

# 16. Asterisk / FreePBX

Asterisk owns:

```text
SIP
PJSIP
RTP
Call routing
Dialplan
IVR execution
Queues
Extensions
Voicemail
Recording
Conference
```

Do not implement telephony behavior in Laravel when Asterisk should handle it.

---

# 17. Angular

Angular owns:

```text
Admin UI
Forms
Dashboards
Configuration
Visualization
Call-flow editing
```

Angular does NOT directly access:

```text
PostgreSQL
Redis
Asterisk internals
AI provider credentials
```

---

# 18. Database Rule

PostgreSQL is the application source of truth.

AI agents and frontend applications must never receive unrestricted database access.

Correct:

```text
Client
 ↓
Laravel
 ↓
Authorization
 ↓
Repository / Service
 ↓
PostgreSQL
```

Incorrect:

```text
Flutter
 ↓
PostgreSQL
```

Incorrect:

```text
AI
 ↓
PostgreSQL
```

---

# 19. AI Tool Rule

AI must interact with the application through controlled tools.

Correct:

```text
AI
 ↓
Tool Request
 ↓
Validation
 ↓
Authorization
 ↓
Tenant Check
 ↓
Business Logic
 ↓
Result
```

Example:

```text
book_appointment()
```

The AI should not be given direct SQL access.

---

# 20. Multi-Tenant Rule

Every tenant-owned resource must be tenant-scoped.

Example:

```text
tenant_id
```

must be considered when working with:

```text
Users
Businesses
Extensions
DIDs
Calls
Recordings
AI agents
Knowledge
Automations
```

Never trust:

```text
tenant_id
```

supplied by the frontend without validating the authenticated user's tenant.

---

# 21. Security Rule

Security must be enforced server-side.

Never rely on:

```text
Flutter hiding a button
Angular hiding a menu
```

for authorization.

Example:

```text
Angular:
Hide Delete User button.

Laravel:
Reject unauthorized DELETE request.
```

Both are required.

---

# 22. Secrets Rule

Never place secrets in source code.

Never commit:

```text
API keys
SIP passwords
Database passwords
Private keys
JWT secrets
Cloud credentials
SSL private keys
```

Use:

```text
.env
Environment variables
Secret manager
Docker secrets
```

depending on environment.

---

# 23. Example Environment File

Commit:

```text
.env.example
```

Never commit:

```text
.env
```

Example:

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

# 24. Error Handling

Never silently ignore errors.

Bad:

```text
try:
    something()
except:
    pass
```

Good:

```text
Catch
Log
Classify
Recover or report
```

Every important failure should have:

```text
Error
Context
Correlation ID where appropriate
User-safe message
Developer log
```

---

# 25. No Silent Fallbacks

Do not silently change behavior.

Bad:

```text
AI failed
↓
Pretend it succeeded
```

Good:

```text
AI unavailable
↓
Transfer to human / fallback flow
```

For telephony:

```text
AI unavailable
        ↓
Business-defined fallback
        ↓
Human / voicemail / message
```

---

# 26. API Rules

All APIs should use:

```text
/api/v1/
```

Example:

```text
/api/v1/businesses
/api/v1/users
/api/v1/extensions
/api/v1/calls
/api/v1/ai-agents
```

API responses should be predictable.

Do not randomly change response formats between endpoints.

---

# 27. API Contract First

Before implementing frontend integration:

```text
Define API
 ↓
Define request
 ↓
Define response
 ↓
Define errors
 ↓
Implement backend
 ↓
Implement client
```

Example:

```text
POST /api/v1/ai-agents
```

Define:

```text
Request:
name
personality
language
voice
instructions
```

Response:

```text
id
name
status
created_at
```

---

# 28. Backward Compatibility

Do not break existing APIs without a deliberate migration.

Bad:

```text
Change:
phone_number
```

to:

```text
number
```

and expect all clients to continue working.

Instead:

```text
Version
Deprecate
Migrate
Remove
```

---

# 29. Database Migration Rules

Never manually modify production database schema without a migration.

Every schema change requires:

```text
Migration
Model update
Tests
Rollback consideration
```

Example:

```text
add_tenant_id_to_calls_table
```

not:

```text
Manually edit production database
```

---

# 30. Database Safety

Be careful with:

```text
DELETE
DROP
TRUNCATE
```

especially in multi-tenant systems.

Never execute destructive commands against production without explicit approval.

---

# 31. Indexing Rule

Add indexes for fields frequently used for:

```text
Filtering
Joining
Tenant isolation
Sorting
Searching
```

Especially:

```text
tenant_id
business_id
user_id
call_id
created_at
status
```

Do not blindly index every column.

---

# 32. Flutter Architecture

Use:

```text
UI
 ↓
BLoC/Cubit
 ↓
Repository
 ↓
Service/Data Source
```

Example:

```text
CallScreen
 ↓
CallBloc
 ↓
CallRepository
 ↓
SipService
```

Do not put business logic inside widgets.

---

# 33. Flutter State Management

Avoid:

```text
Large setState-based architecture
```

Prefer:

```text
BLoC/Cubit
```

for application state.

A widget should primarily:

```text
Display state
Send events
React to state
```

---

# 34. Flutter Single Source of Truth

Do not duplicate the same state across multiple unrelated places.

Example:

Bad:

```text
CallScreen has call state
HomeScreen has another call state
Service has another call state
```

Prefer:

```text
Call State
     ↓
Single authoritative state
     ↓
Multiple UI consumers
```

---

# 35. SIP Abstraction Rule

Do not allow the entire Flutter application to depend directly on a specific SIP package.

Use an abstraction:

```text
SipService
```

or equivalent interface.

Then:

```text
SipService
    ↓
SipRepository
    ↓
CallBloc
```

This makes it possible to replace the SIP implementation later.

---

# 36. Asterisk Abstraction Rule

Laravel should communicate with PBX functionality through an abstraction where practical.

Example:

```text
PBXService
```

Implementation:

```text
AsteriskPBXService
```

Future:

```text
OtherPBXService
```

This protects the application from becoming permanently coupled to one PBX implementation.

---

# 37. Provider Abstraction

External providers should be isolated.

Examples:

```text
AIProvider
StorageProvider
EmailProvider
SMSProvider
CalendarProvider
SIPProvider
```

Example:

```text
AIProvider
 ├── OpenAIProvider
 ├── LocalModelProvider
 └── FutureProvider
```

Do not scatter provider-specific code throughout the application.

---

# 38. AI Provider Rule

Never hardcode an AI provider into every feature.

Bad:

```text
Every service directly calls OpenAI.
```

Better:

```text
Application
 ↓
AI Service
 ↓
AI Provider Interface
 ↓
Provider
```

This allows future provider changes.

---

# 39. AI Prompt Rule

AI prompts are application configuration, not random strings scattered across code.

Prefer:

```text
Agent configuration
 ↓
System instructions
 ↓
Personality
 ↓
Business knowledge
 ↓
Tools
 ↓
Runtime context
```

Keep prompts versioned where practical.

---

# 40. AI Hallucination Rule

AI must not invent business information.

If information is unavailable:

```text
I don't have that information.
```

or:

```text
Let me connect you with someone who can help.
```

is preferable to hallucinating an answer.

---

# 41. AI Tool Permission Rule

Every tool must define:

```text
Name
Purpose
Input
Output
Required permission
Tenant scope
Failure behavior
Audit requirements
```

Example:

```text
book_appointment

Permission:
appointment:create

Tenant:
current tenant only

Failure:
Tell caller booking could not be completed.
```

---

# 42. AI Memory Rule

Do not store everything forever.

Memory must have:

```text
Purpose
Scope
Retention
Privacy
Deletion
```

Possible scopes:

```text
Call session
Customer
Business
Agent
```

---

# 43. Recording Rule

Recordings are sensitive data.

Every recording operation must consider:

```text
Tenant
Authorization
Consent
Retention
Storage
Encryption
Deletion
Audit
```

Never expose recordings through predictable public URLs.

---

# 44. File Upload Rule

Uploaded files must be validated.

Check:

```text
File type
File size
Content
Tenant ownership
Malicious content
Processing status
```

Never trust:

```text
filename
MIME type supplied by client
file extension alone
```

---

# 45. Logging Rule

Logs should help answer:

```text
What happened?
When?
Which tenant?
Which user?
Which call?
Which request?
Which service?
Why did it fail?
```

Use correlation IDs where useful.

Example:

```text
request_id
call_id
tenant_id
```

Do not log secrets.

---

# 46. Telephony Logging

For call-related problems capture enough information to debug:

```text
Call ID
Direction
DID
Extension
Tenant
Timestamp
Call state
SIP response
Failure reason
```

Do not log sensitive credentials.

---

# 47. Call State Rule

Call states must be explicit.

Example:

```text
idle
calling
ringing
connected
on_hold
transferring
transferred
completed
missed
failed
```

Do not create random strings throughout the codebase.

Use a centralized enum/state definition.

---

# 48. Event-Driven Design

Use events where they improve decoupling.

Example:

```text
CallCompleted
      ↓
 ┌────┼────────┐
 ↓    ↓        ↓
Record Summary Automation
```

Avoid tightly coupling every feature together.

---

# 49. Background Jobs

Long-running tasks should not block normal API requests.

Examples:

```text
Recording processing
Transcript generation
AI summary
Document ingestion
Embeddings
Email
SMS
Analytics
```

Use:

```text
Queue
Worker
Job
```

rather than keeping HTTP requests open unnecessarily.

---

# 50. AI Realtime Rule

Realtime voice calls are latency-sensitive.

Do not perform slow operations synchronously unless required.

Prefer:

```text
Realtime conversation
        ↓
Fast tool
        ↓
Immediate response
```

For slow tasks:

```text
Queue
 ↓
Background processing
```

---

# 51. Performance Rule

Do not optimize based on guesses.

First:

```text
Measure
 ↓
Identify bottleneck
 ↓
Optimize
 ↓
Measure again
```

Never introduce:

```text
microservices
Go
Kubernetes
caching everywhere
database sharding
```

simply because they sound scalable.

---

# 52. Microservice Rule

Default:

```text
Simple architecture
```

Split a service only when there is a concrete reason such as:

```text
Independent scaling
Independent deployment
Resource isolation
Failure isolation
Team ownership
```

---

# 53. Go Rule

Go is optional.

Do not introduce Go into the MVP unless there is a demonstrated requirement.

Potential future uses:

```text
High-performance workers
Realtime infrastructure
Media services
High-concurrency services
```

Until then:

```text
Laravel → Control Plane
Python → AI
Asterisk → Telephony
```

---

# 54. Kubernetes Rule

Do not use Kubernetes for the MVP unless there is a genuine infrastructure requirement.

Start with:

```text
Docker
Docker Compose
VM/VPS
```

Scale later based on measurements.

---

# 55. Frontend UX Rule

Admin users should not need to understand telephony internals.

Avoid interfaces like:

```text
PJSIP endpoint
Dialplan context
RTP port range
SIP transport
```

unless an advanced configuration screen is specifically required.

Prefer:

```text
Who should answer?
When?
What happens if nobody answers?
Where should the caller go?
```

---

# 56. Visual Call Flow Rule

Eventually configuration should look like:

```text
Incoming Call
      ↓
Business Hours?
   ↙       ↘
 Yes       No
 ↓          ↓
IVR      Voicemail
 ↓
Sales
 ↓
AI / Human
```

The underlying Asterisk configuration should be hidden behind the platform.

---

# 57. Configuration Rule

Never hardcode business-specific behavior.

Bad:

```text
if company == "ABC":
    transfer_to = "1001"
```

Better:

```text
Business Configuration
 ↓
Call Flow
 ↓
Routing
```

This is critical for multi-tenancy.

---

# 58. Environment Rule

Every environment must be clearly identified:

```text
development
staging
production
```

Never point development Flutter builds to production accidentally.

Use explicit configuration.

---

# 59. Production Safety Rule

AI agents must assume production is dangerous.

Before destructive operations:

```text
STOP
```

Examples:

```text
Drop database
Delete tenant
Delete recordings
Reset PBX
Change production firewall
Rotate production credentials
```

Require explicit human approval.

---

# 60. Git Safety

Never run destructive Git operations unless explicitly requested.

Avoid automatically executing:

```text
git reset --hard
git clean -fd
git push --force
```

Never overwrite another developer's work.

---

# 61. Git Commit Rule

After completing a task:

```text
git status
git diff
tests
```

Review changes before committing.

Commit messages should describe the change.

Examples:

```text
feat(sip): add registration handling
feat(call): add incoming call state
fix(ai): handle transfer failure
test(tenant): enforce tenant isolation
docs(api): document call endpoints
```

---

# 62. No "Final" Files

Never create:

```text
final.dart
final2.dart
new_final.dart
working_final.dart
```

Use proper names.

---

# 63. No Duplicate Implementations

Before creating a new:

```text
Service
Repository
Model
Widget
Controller
Helper
Utility
```

search the project.

There may already be an implementation.

Prefer reuse when appropriate.

---

# 64. Naming Rule

Names should describe purpose.

Good:

```text
CallRepository
TenantService
AiAgentService
RecordingRepository
SipRegistrationState
```

Bad:

```text
Helper2
Manager
Thing
Utils
NewService
```

Avoid generic names.

---

# 65. Comments

Comments should explain:

```text
Why
```

not:

```text
What obvious code does
```

Bad:

```text
// Increment counter
counter++;
```

Good:

```text
// Prevent duplicate call events from creating duplicate call records.
```

---

# 66. Documentation Rule

When behavior changes, update documentation.

Examples:

```text
API changed
→ update API documentation

Architecture changed
→ update architecture

New prerequisite
→ update prerequisites

New sprint
→ update roadmap
```

Documentation is part of the implementation.

---

# 67. Testing Rule

Every meaningful feature must have tests appropriate to its layer.

Examples:

```text
Laravel
→ Unit + Feature tests

Flutter
→ Unit + Widget + BLoC tests

Python
→ Unit + integration tests

Angular
→ Unit/component tests

Telephony
→ SIP/call integration tests
```

---

# 68. Test Before Refactor

Before changing important code:

```text
Run existing tests.
```

Then:

```text
Make change.
 ↓
Run tests again.
```

This provides a safety net.

---

# 69. Regression Rule

A bug fix must include a regression test when practical.

Example:

```text
Bug:
Tenant A could access Tenant B recording.

Fix:
Add tenant authorization.

Test:
Tenant A request for Tenant B recording → 403.
```

---

# 70. Integration Testing Rule

Unit tests are not enough for telephony.

Eventually test:

```text
Flutter
 ↓
SIP
 ↓
Asterisk
 ↓
SIP Trunk
 ↓
PSTN
```

and:

```text
PSTN
 ↓
SIP
 ↓
Asterisk
 ↓
AI
 ↓
Human
```

---

# 71. AI Testing

AI behavior must be tested using scenarios.

Example:

```text
Scenario:
Customer asks business hours.

Expected:
Correct configured hours.

Scenario:
Customer asks unknown question.

Expected:
AI does not invent answer.

Scenario:
Customer asks for human.

Expected:
AI transfers.

Scenario:
Transfer fails.

Expected:
AI follows fallback.
```

---

# 72. Prompt Testing

Maintain a test set for important AI behaviors.

Example:

```text
Greeting
FAQ
Unknown question
Angry customer
Transfer request
Booking request
Invalid request
After-hours call
Unsupported language
Tool failure
```

---

# 73. AI Safety Boundary

The AI agent must never independently decide to perform unrestricted high-impact actions.

Examples requiring controlled permissions:

```text
Delete customer
Refund money
Change account ownership
Change billing
Delete recordings
Modify security settings
```

Tools should explicitly control what the AI can do.

---

# 74. Human Handoff Rule

AI should always have a clear escalation path.

Possible:

```text
AI
 ↓
Transfer
 ↓
Department
```

or:

```text
AI
 ↓
Take message
 ↓
Create task
```

or:

```text
AI
 ↓
Callback request
```

AI must not trap the caller in an endless conversation.

---

# 75. Failure-First Design

For every important feature ask:

```text
What happens when it works?
```

Then:

```text
What happens when it fails?
```

Examples:

```text
AI unavailable
PBX unavailable
SIP unavailable
Database unavailable
Redis unavailable
Storage unavailable
Tool fails
Transfer fails
Network drops
```

---

# 76. Network Failure Rule

Assume networks will fail.

The system should handle:

```text
Timeout
Connection reset
DNS failure
SIP registration failure
RTP failure
API timeout
AI timeout
```

Do not assume perfect connectivity.

---

# 77. Reconnection Rule

Realtime components should have deliberate reconnection behavior.

But avoid:

```text
Infinite reconnect loops
```

Use:

```text
Backoff
Retry limit
State reset
User-visible status
```

---

# 78. Timeout Rule

External calls need timeouts.

Examples:

```text
HTTP
AI
Database
SIP
Storage
Webhooks
```

Never allow an external service to hang forever.

---

# 79. Rate Limiting

Public APIs must eventually have rate limits.

Especially:

```text
Login
Password reset
AI endpoints
File uploads
Webhooks
Public APIs
```

Do not implement arbitrary limits without considering legitimate usage.

---

# 80. Webhook Rule

Webhooks must be:

```text
Authenticated
Validated
Idempotent
Logged
Tenant-aware
```

A webhook may be delivered more than once.

Design accordingly.

---

# 81. Idempotency Rule

Operations that may be retried should be safe to repeat.

Examples:

```text
Create lead
Book appointment
Process webhook
Create recording metadata
```

Avoid duplicate side effects.

---

# 82. Time and Timezone Rule

Never assume:

```text
UTC
```

is the user's business timezone.

Store timestamps consistently, preferably in UTC.

Convert for display/business logic using the configured:

```text
Tenant timezone
Business timezone
```

---

# 83. Business Hours Rule

Business hours must be configuration-driven.

Do not hardcode:

```text
Monday-Friday 9-5
```

Instead:

```text
Business
 ↓
Timezone
 ↓
Business Hours
 ↓
Holiday Calendar
 ↓
Call Flow
```

---

# 84. Localization Rule

AI receptionist should eventually support:

```text
Language
Voice
Greeting
Business terminology
```

Do not assume English is the only language.

However, do not build every language into the MVP unless required.

---

# 85. Data Ownership Rule

Every piece of data must answer:

```text
Who owns this?
```

Possible:

```text
Platform
Tenant
Business
User
Customer
Call
AI Agent
```

This is especially important for multi-tenancy.

---

# 86. Audit Rule

Important actions should be auditable.

Examples:

```text
User created
Extension created
DID changed
IVR published
AI agent changed
Recording accessed
Recording deleted
Automation changed
```

Audit record should capture:

```text
Who
What
When
Tenant
Target
Result
```

---

# 87. Publish / Draft Rule

Configuration-heavy features should support:

```text
Draft
 ↓
Validate
 ↓
Preview
 ↓
Publish
```

Where practical.

Especially:

```text
IVR
Call flows
AI agents
Automations
```

---

# 88. Rollback Rule

Published configurations should eventually be versioned.

Example:

```text
Call Flow v1
Call Flow v2
Call Flow v3
```

If v3 fails:

```text
Rollback → v2
```

Do not require manual Asterisk editing to recover.

---

# 89. No Magic Numbers

Avoid:

```text
if retries > 3
```

without explanation.

Use configuration/constants:

```text
MAX_IVR_RETRIES
AI_TRANSFER_TIMEOUT
CALL_RING_TIMEOUT
```

---

# 90. No Hardcoded Tenant Data

Never put real customer/business information into source code.

Bad:

```text
Company name
Phone number
SIP credentials
Business hours
```

Use configuration/database.

---

# 91. No Hardcoded Production URLs

Avoid:

```text
https://production.example.com
```

inside application logic.

Use environment configuration.

---

# 92. Dependency Rule

Before adding a package:

```text
1. Check whether existing code already solves the problem.
2. Check maintenance status.
3. Check license.
4. Check security.
5. Check platform compatibility.
6. Check bundle/resource impact.
```

Do not add dependencies for trivial functionality.

---

# 93. License Rule

Every third-party dependency must have a known license.

Record important dependencies.

Pay particular attention to:

```text
AGPL
GPL
LGPL
Apache
MIT
BSD
```

Do not assume open-source code is automatically safe for commercial redistribution.

---

# 94. Reference Repository Rule

The reference AI receptionist project is an architectural/reference source.

Do:

```text
Study
Understand
Compare
Learn
Reimplement where appropriate
```

Do not:

```text
Blindly copy large sections of code.
```

Always check license and dependency obligations.

---

# 95. No Architecture Drift

If an AI agent proposes:

```text
Replace Laravel with Go
Replace Asterisk with custom SIP server
Replace PostgreSQL with MongoDB
Add Kubernetes
Add 12 microservices
```

it must explain:

```text
Why?
What problem does this solve?
What complexity does it introduce?
What is the migration cost?
```

Do not change architecture because a technology is trendy.

---

# 96. Technology Selection Rule

Choose technology based on:

```text
Requirement
Reliability
Maintainability
Performance
Security
Cost
Developer expertise
Open-source ecosystem
```

not:

```text
"Hype"
```

---

# 97. Cost Awareness

Every architectural decision should consider:

```text
Server cost
AI cost
SIP/DID cost
Storage
Bandwidth
Operational complexity
Developer time
```

A technically impressive architecture that is too expensive to operate is not a good architecture.

---

# 98. Free/Open-Source Rule

Prefer open-source/self-hosted components where they make sense.

But remember:

```text
Open source
      ≠
Free operations
```

Telephony and AI may still generate usage costs.

---

# 99. Scalability Rule

Build for scalability without prematurely building massive infrastructure.

Target evolution:

```text
1 business
   ↓
10 businesses
   ↓
100 businesses
   ↓
1000+ businesses
```

At each stage:

```text
Measure
 ↓
Identify bottleneck
 ↓
Scale that component
```

---

# 100. No Premature Optimization

Do not optimize:

```text
Before measurement
```

If 100 calls work comfortably on one server:

```text
Do not build a 20-server cluster.
```

---

# 101. No Premature AI Fine-Tuning

Do not fine-tune models simply because:

```text
"We are building AI."
```

First use:

```text
Prompt
 ↓
Knowledge
 ↓
Tools
 ↓
Memory
```

Fine-tuning should solve a demonstrated problem.

---

# 102. AI Knowledge Rule

Business knowledge belongs in a knowledge system, not an enormous system prompt.

Prefer:

```text
Agent Instructions
+
Knowledge Base
+
Tools
```

rather than:

```text
One gigantic prompt containing the entire business.
```

---

# 103. AI Agent Configuration Rule

AI agent configuration should be data-driven.

Example:

```text
Agent
 ├── Identity
 ├── Instructions
 ├── Personality
 ├── Voice
 ├── Language
 ├── Knowledge
 ├── Tools
 ├── Permissions
 ├── Hours
 └── Escalation
```

Do not hardcode one global receptionist.

---

# 104. Multi-Agent Rule

Multiple agents should be specialized when useful.

Example:

```text
Receptionist
Sales
Support
Booking
Billing
```

Do not create multiple agents simply to increase complexity.

---

# 105. Automation Rule

Automations must follow:

```text
Trigger
 ↓
Condition
 ↓
Action
```

They should be:

```text
Observable
Retryable
Auditable
Tenant-scoped
```

---

# 106. Background Worker Rule

Workers must be idempotent where possible.

If:

```text
Job runs twice
```

the system should not accidentally:

```text
Send two identical customer messages
Create two appointments
Duplicate CRM records
```

---

# 107. Queue Rule

Queues should have:

```text
Retry policy
Maximum attempts
Failure handling
Dead-letter strategy where appropriate
Logging
```

Do not retry forever.

---

# 108. Code Generation Rule

AI-generated code must be treated as:

```text
UNTRUSTED UNTIL REVIEWED
```

The developer must understand what is being added.

Never merge code simply because:

```text
"It compiles."
```

---

# 109. Compilation Is Not Completion

This:

```text
Build succeeds
```

does NOT mean:

```text
Feature works
```

Completion requires:

```text
Build
+
Tests
+
Behavior verification
+
Error handling
```

---

# 110. AI Agent Output Format

After completing a task, the coding agent should report:

```text
## Completed

- What was implemented
- Files changed
- Tests added
- Tests executed

## Verification

- Build: PASS/FAIL
- Tests: PASS/FAIL
- Manual test: PASS/FAIL

## Notes

- Important implementation decisions

## Remaining

- Known issues
- Follow-up tasks
- Blockers
```

---

# 111. Before Asking AI to Code

Use prompts with:

```text
Context
Goal
Current architecture
Files
Constraints
Acceptance criteria
Testing requirements
```

Example:

```text
Context:
We are implementing Sprint 4.

Goal:
Implement SIP registration.

Architecture:
Flutter → BLoC → Repository → SipService.

Constraints:
Do not modify unrelated modules.
Do not use setState for application state.

Acceptance:
Flutter can register against the test Asterisk server.

Testing:
Add registration-state tests.
```

---

# 112. Bad AI Prompt

Avoid:

```text
Build the entire SIP system.
```

Too broad.

---

# 113. Good AI Prompt

Prefer:

```text
Implement SIP registration for Sprint 4.

Read:
00-README.md
02-ARCHITECTURE.md
04-SPRINT-ROADMAP.md
05-VIBE-CODING-RULES.md

Inspect the existing Flutter structure.

Implement only:
- SipService registration
- Repository integration
- BLoC registration state
- UI registration status

Do not modify unrelated features.

Add tests.

Run tests.

Report changed files and blockers.
```

---

# 114. Context Rule for AI

Do not dump the entire repository into every AI prompt.

Give the agent:

```text
Relevant documentation
Relevant files
Relevant interfaces
Current task
Acceptance criteria
```

This reduces hallucinations and unnecessary changes.

---

# 115. AI Planning Rule

For medium/large tasks, AI should first provide a short plan.

Example:

```text
Plan:
1. Inspect SIP service.
2. Add registration interface.
3. Implement repository method.
4. Add BLoC event/state.
5. Update UI.
6. Add tests.
7. Run verification.
```

Then implement.

---

# 116. No Overengineering

If a feature can be implemented with:

```text
1 service
1 repository
1 BLoC
```

do not create:

```text
7 abstractions
12 factories
8 interfaces
```

without a reason.

---

# 117. No Underengineering

Do not put everything into:

```text
one giant file
```

Example:

```text
AIReceptionistEverything.dart
```

Separate responsibilities logically.

---

# 118. File Size Rule

Large files should be reviewed for separation when they become difficult to understand.

But do not split files simply to satisfy an arbitrary line count.

Optimize for:

```text
Cohesion
Readability
Testability
Maintainability
```

---

# 119. Circular Dependency Rule

Avoid:

```text
Service A → Service B
Service B → Service A
```

Use:

```text
Interface
Event
Repository
Application service
```

where appropriate.

---

# 120. Dependency Injection

Use dependency injection for replaceable infrastructure.

Examples:

```text
Repositories
Services
AI providers
Storage
SIP
PBX
```

This improves:

```text
Testing
Replacement
Configuration
Maintainability
```

---

# 121. Testability Rule

If a component is difficult to test:

```text
Ask why.
```

Avoid tightly coupling business logic to:

```text
UI
network
database
provider SDK
```

---

# 122. Security Review Before Merge

For changes involving:

```text
Authentication
Authorization
Tenant data
Recordings
AI tools
File uploads
Webhooks
Payments
Secrets
PBX
```

perform an explicit security review.

---

# 123. Telephony Review Before Merge

For changes involving:

```text
SIP
PJSIP
RTP
Dialplan
IVR
Transfers
Recording
Queues
WebRTC
```

test actual call behavior where possible.

---

# 124. AI Review Before Merge

For AI changes verify:

```text
[ ] Prompt behavior
[ ] Tool permissions
[ ] Tenant isolation
[ ] Failure handling
[ ] Hallucination behavior
[ ] Transfer behavior
[ ] Logging
[ ] Cost implications
```

---

# 125. Performance Review Before Merge

Ask:

```text
Does this introduce:
- N+1 queries?
- unnecessary API calls?
- blocking operations?
- memory growth?
- repeated AI calls?
- excessive logging?
- unnecessary network traffic?
```

Fix real problems, not theoretical ones.

---

# 126. Cost Review for AI

Every AI feature should consider:

```text
Tokens
Audio duration
Model usage
Tool calls
Retries
Transcription
Storage
```

Avoid loops that accidentally generate unlimited AI calls.

---

# 127. AI Call Budget

The system should eventually track:

```text
tenant
agent
call
duration
model
usage
estimated cost
```

This enables:

```text
Usage limits
Billing
Cost analytics
Abuse prevention
```

---

# 128. Observability Rule

Every important distributed operation should be traceable.

Example:

```text
Call ID
 ↓
Asterisk
 ↓
Laravel
 ↓
AI
 ↓
Tool
 ↓
Database
```

A developer should be able to trace what happened.

---

# 129. Documentation-as-Code

Keep technical documentation in Git.

Recommended:

```text
docs/
├── architecture/
├── api/
├── telephony/
├── ai/
├── deployment/
└── operations/
```

Documentation changes should be reviewed like code.

---

# 130. Decision Log

Important architectural decisions should be recorded.

Example:

```text
ADR-001:
Use Asterisk as telephony engine.

ADR-002:
Use Laravel as control plane.

ADR-003:
Use Python for AI.

ADR-004:
Use PostgreSQL as source of truth.
```

This prevents future AI agents from repeatedly questioning settled decisions.

---

# 131. AI Must Respect Existing Decisions

If an architecture decision already exists:

```text
Do not silently replace it.
```

If a better alternative appears:

```text
Propose it separately.
```

Include:

```text
Current approach
Alternative
Advantages
Disadvantages
Migration cost
Recommendation
```

---

# 132. Change Proposal Rule

Major changes require a proposal before implementation.

Major changes include:

```text
Changing database
Changing PBX
Changing AI architecture
Changing authentication
Changing multi-tenancy model
Adding major infrastructure
Adding microservices
```

---

# 133. Production Data Rule

AI coding agents should assume:

```text
Production data is valuable.
```

Never:

```text
Reset production database
Delete production recordings
Run destructive migration
```

without explicit authorization.

---

# 134. Customer Data Rule

Never place customer data into:

```text
Git commits
Test fixtures
Screenshots
Public logs
AI prompts
Documentation
```

unless intentionally anonymized or explicitly required.

---

# 135. Test Data Rule

Use synthetic data.

Example:

```text
Test Company
test@example.com
+15550000000
```

Do not use real customer information.

---

# 136. Demo Environment

Maintain a separate demo/staging environment.

Recommended:

```text
Development
    ↓
Staging/Demo
    ↓
Production
```

Never test experimental AI behavior directly on production customers.

---

# 137. Release Rule

Before production release:

```text
Code
 ↓
Tests
 ↓
Staging
 ↓
Smoke Test
 ↓
Security Review
 ↓
Backup Verification
 ↓
Release
```

---

# 138. Rollback Rule

Every production release should have a rollback strategy.

Possible:

```text
Previous container image
Previous application version
Previous database migration
Previous call-flow version
Previous AI configuration
```

---

# 139. Smoke Test

After deployment verify:

```text
[ ] Login
[ ] API
[ ] Database
[ ] Admin
[ ] SIP registration
[ ] Inbound call
[ ] Outbound call
[ ] Audio
[ ] IVR
[ ] Recording
[ ] AI
[ ] Transfer
```

---

# 140. Emergency Rule

If production calling breaks:

```text
STOP feature development.
```

Priority becomes:

```text
Restore service
 ↓
Identify cause
 ↓
Fix
 ↓
Verify
 ↓
Document
```

Production reliability always has priority over new features.

---

# 141. Priority System

Use:

```text
P0 = Production outage / severe security issue
P1 = Critical functionality broken
P2 = Important bug
P3 = Normal feature
P4 = Nice-to-have
```

Always handle:

```text
P0 → P1 → P2 → P3 → P4
```

---

# 142. Feature Priority

When overwhelmed, use:

```text
NOW
NEXT
LATER
```

Example:

```text
NOW:
SIP registration

NEXT:
Incoming calls

LATER:
AI video
```

Do not work on LATER while NOW is broken.

---

# 143. ADHD-Friendly Task Format

Every development task should be reduced to:

```text
TASK

Goal:
One sentence.

Do:
[ ] Step 1
[ ] Step 2
[ ] Step 3

Done when:
One measurable result.

Do not:
One or two important constraints.
```

Example:

```text
TASK

Goal:
Make Flutter register with Asterisk.

Do:
[ ] Create SipService
[ ] Connect credentials
[ ] Register
[ ] Emit state
[ ] Show state
[ ] Test

Done when:
Flutter displays REGISTERED.

Do not:
Modify authentication or unrelated UI.
```

---

# 144. Avoid Context Switching

Do not jump between:

```text
Flutter
AI
Angular
PBX
Database
```

every few minutes.

Finish or deliberately pause the current task.

---

# 145. Daily AI Coding Workflow

Recommended:

```text
1. Pick one task.
2. Read relevant documentation.
3. Inspect code.
4. Ask AI for a plan.
5. Implement.
6. Run tests.
7. Manually verify.
8. Review diff.
9. Commit.
10. Update task status.
```

---

# 146. End-of-Day Status

Record:

```text
DONE
BLOCKED
NEXT
```

Example:

```text
DONE:
Flutter SIP registration implemented.

BLOCKED:
Incoming call testing requires second SIP endpoint.

NEXT:
Implement incoming call state.
```

---

# 147. Final AI Agent Checklist

Before saying "Done":

```text
[ ] I read the relevant documentation.
[ ] I understood the existing architecture.
[ ] I identified the current sprint.
[ ] I inspected existing code.
[ ] I did not invent requirements.
[ ] I did not modify unrelated code.
[ ] I did not expose secrets.
[ ] I respected tenant boundaries.
[ ] I added appropriate error handling.
[ ] I added/updated tests.
[ ] I ran tests.
[ ] I checked the diff.
[ ] I updated documentation if necessary.
[ ] I reported blockers.
```

---

# 148. Final Developer Checklist

Before merging:

```text
[ ] Feature works
[ ] Tests pass
[ ] No obvious security issue
[ ] No secrets
[ ] No tenant leakage
[ ] No unnecessary dependencies
[ ] No architecture drift
[ ] No unrelated changes
[ ] Documentation updated
[ ] Production impact understood
```

---

# 149. The Vibe Coding Constitution

These rules can be summarized as:

```text
DO NOT GUESS.
DO NOT FAKE.
DO NOT OVERENGINEER.
DO NOT REWRITE WITHOUT A REASON.
DO NOT IGNORE SECURITY.
DO NOT BREAK TENANT ISOLATION.
DO NOT PUT SECRETS IN CODE.
DO NOT SKIP TESTING.
DO NOT IGNORE BLOCKERS.
DO NOT CHANGE ARCHITECTURE SILENTLY.
DO NOT BUILD FEATURES OUT OF ORDER.
```

Instead:

```text
READ
UNDERSTAND
PLAN
IMPLEMENT
TEST
VERIFY
DOCUMENT
COMMIT
```

---

# 150. Final Principle

> **Vibe coding is not "let AI build whatever it wants."**

It means:

```text
Human defines:
Architecture
Requirements
Priorities
Constraints
Acceptance criteria

AI handles:
Implementation
Iteration
Debugging
Testing assistance
Documentation
Refactoring
```

The human controls **what and why**.

The AI helps execute **how**.

---

# 151. Project Commandment

Every developer and AI agent working on this project must follow:

```text
                    ┌───────────────┐
                    │  REQUIREMENT  │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │  ARCHITECTURE │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │     PLAN      │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │     CODE      │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │     TEST      │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │    VERIFY     │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │  DOCUMENT     │
                    └───────┬───────┘
                            ↓
                    ┌───────────────┐
                    │    COMMIT     │
                    └───────────────┘
```

**Build small. Test constantly. Never guess. Never fake. Keep the architecture boring and the product powerful.**
