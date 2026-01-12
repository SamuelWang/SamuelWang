# SRS Document Strategy for Software Versions

If you are working within a specific methodology (like Waterfall, Agile, or V-Model) or a regulated industry (like healthcare or finance), as this heavily influences documentation standards.

However, speaking generally about software engineering best practices: **The answer lies somewhere in the middle.**

Technically, a product should have a **Single Source of Truth**, but that source evolves over time. Here is the professional breakdown of how SRS (Software Requirements Specification) documents are handled across different life cycles.

## 1. The Best Practice: One "Living" Document (Version Controlled)

In modern software development, we aim for a **Single Source of Truth**. You do not want five different disconnected Word documents floating around, as this leads to "documentation drift" and confusion.

Instead of creating a brand new, empty SRS for Version 2.0, you maintain **one master SRS** that evolves.

* **Versioning:** When Version 1.0 of the software is released, you "baseline" or tag the SRS at version 1.0.
* **Updates:** For Version 2.0 of the software, you open the master SRS, track changes (deltas), add new features, mark deprecated ones, and then save that as SRS version 2.0.
* **Result:** You have a history of files (SRS v1, SRS v2, SRS v3), but they represent the continuous evolution of the same product.

## 2. The "Snapshot" Strategy (Regulated Industries/Waterfall)

In strict Waterfall environments or safety-critical industries (e.g., Medical Devices (IEC 62304) or Automotive (ISO 26262)), distinct documentation is often legally required for every major release.

* **The Workflow:** You finalize "SRS v1.0" and sign it off physically or digitally. It is then locked (read-only).
* **The Next Version:** To start development on v2.0, you duplicate v1.0 to create a new file. This new file becomes the SRS for the next release.
* **Why?** Auditors need to see exactly what requirements were active when a specific version of the software was built.

## 3. The Agile Reality: "SRS" is often not a Document

If you are working in Agile (Scrum/Kanban), the concept of a monolithic SRS document often dissolves.

* **Backlogs & Epics:** The "requirements" live in a project management tool (Jira, Azure DevOps, Trello).
* **The "Spec":** The specification is the sum of all User Stories and Acceptance Criteria currently in the backlog or active sprint.
* **Documentation:** Instead of an SRS, teams often maintain a **Wiki (e.g., Confluence)**. This Wiki acts as the "Living SRS," updated constantly without rigid version numbers, while the code releases are versioned strictly.

## Summary Table

| Approach | Document Strategy | Best For |
| --- | --- | --- |
| **Traditional / Waterfall** | Distinct, frozen documents for every major release. | Government, Banking, Contracting. |
| **Iterative / Modern** | One master document using version control (Git for docs). | Enterprise SaaS, Desktop Applications. |
| **Agile / DevOps** | No single document; requirements live in tickets/wikis. | Web Apps, Mobile Apps, Startups. |

## Expert Recommendation

If you are building a product that requires long-term maintenance, I strongly recommend treating your SRS as a **repository-based artifact**, not just a file.

1. Keep the requirements in a central system (or a Markdown file in your Git repo).
2. Tag the state of the requirements whenever you release software.
3. This gives you the best of both worlds: a single evolving truth, but the ability to look back at what the requirements were for previous versions.
