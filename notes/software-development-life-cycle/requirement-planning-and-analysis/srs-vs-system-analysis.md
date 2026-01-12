# SRS vs. System Analysis: What Belongs?

Regarding the question about whether the **Software Requirements Specification (SRS)** should include **system analysis**:

The short answer is: **Generally, no.**

However, the nuance lies in the definition of "analysis." The SRS should contain the **results** of the system analysis, but not the deep-dive investigation, feasibility studies, or internal technical mechanics of the analysis itself.

Here is a detailed breakdown of where the lines are drawn and how to structure this effectively.

## 1. The Core Distinction: Requirements vs. Analysis

To maintain a clean architecture in your documentation, you must distinguish between the "What" and the "Why/How."

* **System Analysis (The Process):** This is the investigation phase. It involves studying the current business, identifying problems, analyzing feasibility, and determining constraints. It often includes analyzing the "As-Is" system versus the "To-Be" system.
* **SRS (The Output):** This document defines **what** the software must do to satisfy the needs discovered during analysis. It is a contract between the client and the developer.

**Rule of Thumb:**

> If the information explains **what the system must achieve** (e.g., "The system shall calculate tax..."), it belongs in the SRS.
> If the information explains **the internal logic, database normalization, or code structure** used to achieve it, it belongs in the **System Design Document (SDD)**.

## 2. What "Analysis" Artifacts DO Belong in an SRS?

While you shouldn't include your raw notes or feasibility studies, certain analysis models are crucial in an SRS because they clarify requirements. These are often considered part of the "analysis," but they serve the requirement definition:

* **Use Case Diagrams & Descriptions:** These are analysis tools, but they are standard in an SRS because they describe functional behavior from a user's perspective.
* **Context Diagrams (Data Flow Diagram Level 0):** This shows the system's boundaries and how it interacts with external entities (users, other APIs). This is essential for the SRS.
* **State Transition Diagrams:** If the system has complex states (e.g., an order moving from 'Pending' to 'Shipped'), this logic is a requirement and belongs here.

## 3. What Should Be Excluded (or Moved)?

The following aspects of "System Analysis" should generally be kept out of the SRS and placed in other documents to ensure the SRS remains readable and testable:

* **Feasibility Study:** Financial, technical, and operational feasibility belongs in a **Business Case** or **Project Charter**.
* **Gap Analysis:** The detailed comparison of "Old System vs. New System" usually belongs in a internal **Business Analysis Report**.
* **Internal Algorithms & Class Diagrams:** These belong in the **System Design Document (SDD)**.

## 4. Recommended Structure (IEEE 830 Standard)

If you follow the industry-standard **IEEE 830** guidelines for an SRS, you will see that "System Analysis" isn't a section. Instead, we use **"Overall Description"** to set the context.

A modern, clean SRS structure looks like this:

1. **Introduction** (Purpose, Scope, Definitions)
2. **Overall Description**

    * *Product Perspective* (Here is where you put the **Context Diagram**—the result of your high-level analysis).
    * *User Classes and Characteristics.*
    * *Operating Environment.*

3. **Specific Requirements** (Functional, Non-functional, Interface requirements).
4. **Appendices** (If you absolutely must include analysis data, put it here so it doesn't clutter the requirements).

## 5. Why Separation Matters

* **Testability:** QA engineers use the SRS to write test cases. If the document is cluttered with analysis on *why* a decision was made, it becomes harder to extract *what* needs to be tested.
* **Clarity for Stakeholders:** Clients care about the deliverable (Requirements), not necessarily the engineering study that led to it.
* **Maintainability:** If the implementation changes but the requirement stays the same, you shouldn't have to rewrite an analysis section in the SRS.

## Summary

The SRS is the **destination**, not the **journey**.

Include the **models** that clarify the requirements (Use Cases, Context Diagrams), but exclude the **investigative analysis** (Feasibility, Gap Analysis, Internal Logic).
