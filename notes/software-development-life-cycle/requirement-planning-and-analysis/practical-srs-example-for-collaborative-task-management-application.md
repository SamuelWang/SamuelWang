# Practical SRS Example for Collaborative Task Management Application

Here is a practical, concrete example of an SRS for a project most developers are familiar with: a **Collaborative Task Management Application** (similar to a simplified Trello or Asana).

You can use this as a template to understand the level of detail and language required.

---

# Software Requirements Specification

**Project Name:** TaskMaster – Collaborative Task Management System
**Version:** 1.0

## 1. Introduction

### 1.1 Purpose

The purpose of this document is to define the requirements for "TaskMaster," a web-based application that allows teams to create, assign, and track tasks in real-time.

### 1.2 Scope

* **In Scope:** User authentication, creating project boards, real-time task updates, commenting system, and email notifications.
* **Out of Scope:** Mobile native apps (iOS/Android), offline mode, billing/subscription management (v1.0 is free-tier only), and third-party integrations (e.g., Slack/Jira).

## 2. Functional Requirements

### 2.1 User Authentication Module

**2.1.1 Registration**

* The system **shall** allow users to register using an email address and password.
* The password **must** meet the following complexity requirements: minimum 8 characters, at least one uppercase letter, and one special character.

**2.1.2 Login**

* The system **shall** authenticate users via JWT (JSON Web Tokens).
* The system **shall** lock the user account for 15 minutes after 5 consecutive failed login attempts.

### 2.2 Task Management Module

**2.2.1 Create Task**

* Users **shall** be able to create a new task with the following mandatory fields: Title, Status (Todo, In Progress, Done), and Priority.
* The system **shall** assign a unique ID (UUID) to every created task.

**2.2.2 Real-Time Updates**

* When a user moves a task from one status column to another, the system **shall** reflect this change on all other connected clients’ screens within 2 seconds without requiring a page refresh.

### 2.3 Search Functionality

* The system **shall** allow users to search for tasks by Title or Assignee.
* The search results **shall** update dynamically as the user types (debounce time: 300ms).

## 3. Non-Functional Requirements

### 3.1 Performance

* **Latency:** API response time for creating or updating a task **shall not exceed** 200ms under a load of 1,000 concurrent users.
* **Rendering:** The initial dashboard load time **shall be under** 1.5 seconds on a standard 4G network.

### 3.2 Security

* **Data at Rest:** All database entries containing user personal information (PII) **must** be encrypted using AES-256 standard.
* **Transport:** All system communications **must** occur over HTTPS (TLS 1.2 or higher).
* **Input Validation:** The backend **must** validate all API inputs to prevent SQL Injection and Cross-Site Scripting (XSS).

### 3.3 Reliability & Availability

* The system **shall** target an uptime of 99.9% during business hours (09:00 - 18:00 EST).
* The database **shall** perform automated backups every 6 hours, with a retention period of 30 days.

## 4. Technical Stack Implications (Expert Analysis)

*Based on the requirements above, here is how a developer would translate this SRS into a tech stack:*

1. **Requirement:** "Real-time updates without page refresh."
    * **Solution:** Requires **WebSockets** (e.g., Socket.io) rather than standard REST HTTP calls.


2. **Requirement:** "Search updates as user types."
    * **Solution:** Requires an efficient search index, potentially **Elasticsearch** or full-text search features in **PostgreSQL**.


3. **Requirement:** "JWT Authentication."
    * **Solution:** Stateless authentication architecture, likely using a middleware in Node.js (Passport.js) or Spring Boot Security.
