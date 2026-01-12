# Writing a Software Requirements Specification

In the world of software engineering, ambiguity is the enemy of delivery. The most common reason projects fail isn't a lack of coding skill; it is a lack of clarity. This is where the **Software Requirements Specification (SRS)** comes into play.

The SRS is not just paperwork, but the contract between the business stakeholders (who define the *value*) and the engineering team (who define the *solution*). It is the single source of truth that prevents scope creep, architectural misalignment, and wasted development cycles.

## 1. The Purpose and Scope

An SRS is a document that describes the nature of a project, software, or application. In simple terms, it dictates **what** the software will do and **how** it is expected to perform.

While formats vary, the industry standard (derived from IEEE 830) generally follows a specific hierarchy. The first section must establish boundaries.

### Defining the Scope

The most critical part of the introduction is defining what is **Out of Scope**.

* **In Scope:** "The system will allow users to upload images up to 5MB."
* **Out of Scope:** "The system will not support video editing or filtering in v1.0."

> **Expert Note:** Clearly defining what you are *not* building is often more important than defining what you *are* building. This protects the team from "feature creep" during the sprint cycles.

## 2. Functional Requirements (The "What")

This section forms the bulk of the document. It describes the specific behaviors and functions of the system. These requirements must be granular enough for a QA engineer to write a test case against.

### Granularity and Logic

Instead of saying "User can buy items," break it down into atomic units:

1. "The system shall check inventory levels before adding an item to the cart."
2. "The system shall lock the inventory item for 15 minutes upon checkout initiation."

### Use Cases and User Stories

Many modern SRS documents incorporate User Stories to provide context.

* **Format:** As a `<role>`, I want to `<action>` so that `<benefit>`.
* **Example:** "As an Admin, I want to force-reset user passwords so that I can secure compromised accounts."

## 3. Non-Functional Requirements (The "How")

This is where the software architecture is defined. The developers look at this section to decide whether to use a Monolith or Microservices, SQL or NoSQL, Python or Go. These are often called "Quality Attributes."

| Attribute | Description | Technical Implication |
| --- | --- | --- |
| **Performance** | Speed and responsiveness. | "API response time must be under 200ms." (Suggests caching strategies like Redis). |
| **Scalability** | Handling growth. | "Must support 10k concurrent connections." (Suggests horizontal scaling/load balancing). |
| **Security** | Data protection. | "Passwords must be hashed with Argon2; Data at rest must be AES-256 encrypted." |
| **Reliability** | Uptime and recovery. | "99.9% availability." (Implies redundancy and failover systems). |

## 4. The Golden Rules of Writing Requirements

To ensure your SRS translates effectively into code, adhere to the **SMART** criteria: **S**pecific, **M**easurable, **A**ttainable, **R**elevant, and **T**ime-bound.

### The Language of Rigor

Ambiguity leads to bugs. Use the terminology defined by **RFC 2119** (Key words for use in RFCs to Indicate Requirement Levels):

* **SHALL / MUST:** Absolute requirements.
* *Good:* "The system **shall** log all failed login attempts."
* *Bad:* "The system should probably track login errors."


* **SHOULD:** Recommended but not critical.
* **MAY:** Optional.

### Avoid "Fluff" Words

Never use subjective adjectives in a technical specification.

* **Avoid:** "Fast," "User-friendly," "Robust," "Easy."
* **Replace with:** "Loads in <1s," "Max 3 clicks to checkout," "Handles exceptions without crashing."

## 5. Architectural Implications

A well-written SRS allows the Lead Developer or Architect to make informed decisions early.

1. **Data Modeling:** If the SRS specifies "Users have dynamic, unstructured profile attributes," the architect knows to reach for a NoSQL solution (like MongoDB) or a JSONB column in PostgreSQL, rather than a rigid relational schema.
2. **Infrastructure:** If the SRS demands "Global low-latency access," the DevOps engineer knows to utilize a Content Delivery Network (CDN) and Edge Computing.

## Conclusion

Writing an SRS is an exercise in logic and communication. It forces stakeholders to think deeply about their needs and allows developers to build with confidence. A document that is specific, measurable, and technically grounded is the first step toward a high-quality software product.
