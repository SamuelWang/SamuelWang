# Requirement Planning & Analysis

In the Software Development Life Cycle (SDLC), Requirement Planning & Analysis is the critical first phase. It is where abstract ideas are transformed into concrete, actionable technical specifications. Skipping or rushing this phase is the leading cause of scope creep, budget overruns, and technical debt later in the project.

## Key Objectives

1. **Feasibility Study:** Determining if the project is technically, financially, and operationally viable.
2. **Requirement Gathering:** Collecting detailed functional requirements (what the system does) and non-functional requirements (how the system performs, e.g., latency, throughput) from stakeholders.
3. **Risk Analysis:** Identifying potential technical bottlenecks, security vulnerabilities, or resource constraints early on.

## Core Activities

* **Stakeholder Interviews:** Engaging with users, business owners, and domain experts to understand the "pain points" the software must solve.
* **SRS Creation:** Drafting the **Software Requirement Specification (SRS)** document. This acts as the "source of truth" for developers and QA engineers throughout the lifecycle.
* **Tech Stack Selection:** Deciding on languages (e.g., TypeScript vs. Rust), databases (PostgreSQL vs. MongoDB), and infrastructure (AWS vs. Azure) based on the specific constraints identified.
* **High-Level Design (HLD):** Sketching out the initial system boundaries, data flow, and component interactions.

## Why It Matters

> "The cost to fix a bug found during the requirements phase is orders of magnitude lower than fixing it during production."

A solid planning phase ensures that the development team builds the *right* product, rather than just building the product *right*. It aligns business goals with engineering reality.

## Best practices for the Requirement Planning & Analysis

### 1. Stakeholder Identification & Engagement

You cannot gather requirements if you don't know who requires them.

* **Identify all players:** Don't just talk to the CEO. Talk to the end-users, the support team, the security compliance officer, and the investors.
* **Distinguish Needs vs. Wants:** Stakeholders often express solutions ("I need a button here") rather than problems ("I need to submit this form quickly"). Your job is to dig for the underlying **need**.
* **Active Listening:** Use techniques like repeating back what you heard to ensure alignment. "So, if I understand correctly, the system must handle 10k concurrent users during flash sales?"

### 2. Rigorous Requirement Classification

To build a scalable architecture, you must categorize requirements clearly.

* **Functional Requirements:** What the system *does*. (e.g., "A user can log in via OAuth.")
* **Non-Functional Requirements (NFRs):** How the system *performs*. These are critical for architecture.
  * *Scalability:* Can it handle growth?
  * *Security:* Is data encrypted at rest and in transit?
  * *Reliability:* What is the expected uptime (e.g., 99.9%)?

* **Domain Constraints:** Legal or physical limitations (e.g., "Data must remain within EU servers for GDPR compliance").

### 3. Clear & Unambiguous Documentation

Ambiguity is the enemy of development.

* **The SRS (Software Requirements Specification):** This is your bible. It should be detailed enough that a developer can write code without constant clarification, but high-level enough to be understood by business stakeholders.
* **User Stories & Acceptance Criteria:**
  * Format: *As a [role], I want [feature], so that [benefit].*
  * **Definition of Done (DoD):** Explicitly state what constitutes a completed task (e.g., "Code reviewed, Unit tests passed, deployed to staging").

* **Visual Models:** Use flowcharts, UML diagrams, or wireframes. A visual representation often uncovers logic gaps that text misses.

### 4. Feasibility Analysis (TELOS)

Before writing a single line of code, validate that the project is realistic.

* **T**echnical: Do we have the technology and skills?
* **E**conomic: Can we build it within the budget?
* **L**egal: Does it violate any laws or IP rights?
* **O**perational: Will the organization actually use it?
* **S**cheduling: Can we meet the deadline?

### 5. Prioritization (MoSCoW Method)

Resources are rarely infinite. You must decide what matters most.

* **M**ust have: Critical for launch (MVP).
* **S**hould have: Important but not vital for the very first release.
* **C**ould have: Desirable, but can be sacrificed if needed.
* **W**on't have: Agreed exclusions for this iteration.

### 6. Requirement Traceability

Maintain a **Traceability Matrix**. This links every requirement to a specific business goal, and eventually to a specific test case. This ensures:

1. No requirement is orphaned (built for no reason).
2. No requirement is untested.
3. Impact analysis is easier when scope changes occur.

### Summary Checklist for Success

* [ ] Have all stakeholders signed off on the SRS?
* [ ] Are the Non-Functional Requirements (NFRs) quantifiable? (e.g., "Fast loading" is bad; "Loads under 200ms" is good).
* [ ] Is the scope clearly defined to prevent "scope creep"?
