# Example Walkthrough: Task Initialization (Content Discovery)

Summary: Demonstrates the content discovery workflow for `task-init`. Decoupled from structural formatting—use this example for **content** only. The final output must be formatted using the designated structural skill (`hierarchical-outline`).

---

### Step 1: Ingest User Request
**User**:
> "I want to add rate limiting to our API endpoints so clients cannot overwhelm the system with rapid requests."

---

### Step 2: Context Ingestion (No Codebase Exploration)
The agent ingests the request and identifies key content without inspecting code or repository files:
- **Task Name**: `api-rate-limiting`
- **Identified Content Needs**:
  - Request volume threshold and time window
  - Client identification method
  - Rejection behavior and status signaling

---

### Step 3: Interactive Clarification
The agent clarifies requirements content with the user:
> "What threshold and time window should trigger rate limiting, how should a client be identified, and what should be returned when the limit is exceeded?"
>
> **User**: "Limit to 100 requests per minute per client IP address. Return HTTP 429 with a standard retry-after indication. Existing endpoint behaviors and contracts must not change."

---

### Step 4: Pre-Save Self-Check (Abstraction Verification)
Before drafting, verify the content adheres to abstraction rules:
- [x] Zero code snippets or signatures
- [x] Zero file paths or module names
- [x] Zero class, function, or method names
- [x] Zero layer-by-layer breakdowns
- [x] Abstract terms used ("inbound request path", "client identifier", "external dependency")
- [x] Preserves existing contracts by default

---

### Step 5: Content Captured
> [!NOTE]
> This section records the raw captured **content**. When formatting the final artifact, do not copy this flat presentation; structure the document using the `hierarchical-outline` skill.

- **Title**: Requirements Specification: API Rate Limiting
- **Summary**: Throttles inbound client requests exceeding rate thresholds while preserving existing endpoint behavior and contracts.
- **Metadata**:
  - Task Name: api-rate-limiting
  - Status: Draft
  - Target Directory: plan/api-rate-limiting/
- **Problem Statement**:
  The service currently processes all incoming requests without volume restrictions. A surge in request frequency from a single client can exhaust system resources and degrade service availability for other users. An automated throttling mechanism is needed to enforce fair usage.
- **Task**:
  Enforce rate limiting on all public inbound request paths by rejecting traffic from any client identifier that exceeds the configured request frequency threshold, while preserving existing response contracts for compliant traffic.
- **Scope**:
  - In Scope:
    - Tracking request frequency per client identifier across a sliding time window.
    - Intercepting and rejecting requests exceeding the frequency threshold with timing signals.
    - Maintaining unaltered processing for compliant traffic.
  - Out of Scope:
    - User authentication or account tier-based dynamic quotas.
    - Client blacklisting or permanent IP blocking.
- **Requirements**:
  1. Count inbound requests grouped by client identifier within an active 60-second window.
  2. Reject further requests from a client identifier once 100 requests have occurred within the active 60-second window.
  3. Include timing guidance and retry indications in rejection responses.
  4. Automatically resume normal request processing once the active time window rolls over.
  5. Ensure all existing endpoints and interface contracts remain unchanged for compliant traffic.
- **Acceptance Criteria**:
  - [ ] AC 1: Sending up to 100 requests within 60 seconds from a single client IP results in normal responses (HTTP 2xx).
  - [ ] AC 2: The 101st request within 60 seconds from the same client IP receives an HTTP 429 response with retry delay indication.
  - [ ] AC 3: Requests from separate client IPs do not impact each other's rate counters.
  - [ ] AC 4: Once the 60-second window elapses, the client IP can successfully make requests again.
- **Resolved Ambiguities**:
  - Rate Limit Threshold: 100 requests per minute per client IP. Rationale: User-defined operating constraint.
  - Exceeded Response Behavior: Return HTTP 429 with retry delay indication. Rationale: Standard client backoff signaling requested by user.

---

### Step 6: User Approval & Final Persistence
Once reviewed and approved by the user, the finalized document is formatted using the `hierarchical-outline` skill and saved to `plan/api-rate-limiting/requirements.md`.
