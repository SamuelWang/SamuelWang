# Consistency Patterns

In distributed software systems, especially modern web applications, data is often stored across multiple servers or databases to ensure reliability and speed. **Consistency** refers to how we ensure that all these different parts of the system agree on what the data currently looks like.

When you update data in one place, it takes time for that update to reach all other places. Consistency patterns help manage this delay based on what your application needs.

Here are the primary consistency models and patterns used in system design:

## 1. Strong Consistency (Strict Consistency)

In this model, after a piece of data is updated, any subsequent read operation will return that new, updated value, no matter which server you ask.

* **How it works:** The system locks the data during an update. It waits until all copies (replicas) of the data are updated before allowing any new read or write requests.
* **Use case:** Financial transactions, billing systems, or inventory management where showing outdated data is unacceptable.
* **Trade-off:** It is slower (higher latency) and can cause the system to be temporarily unavailable if a network issue prevents replicas from communicating.

## 2. Eventual Consistency

This is the most common model in large-scale web applications. It guarantees that if no new updates are made to a piece of data, *eventually* all servers will reflect the last updated value.

* **How it works:** The system accepts the update on one server and immediately says "success" to the user. It then syncs the data to other servers in the background.
* **Use case:** Social media feeds, video view counts, or caching systems (like CDNs). If a user sees a post a few seconds later than someone else, it does not break the application.
* **Trade-off:** Users might read stale (outdated) data for a short period. However, the system is highly available and responds very quickly.

## 3. Client-Centric Consistency Patterns

These patterns sit between strong and eventual consistency, focusing on the user's experience rather than the whole system's state:

* **Read-Your-Own-Writes:** Guarantees that once a user updates a record, they will always see that update if they refresh the page, even if other users temporarily see the old version.
* **Monotonic Reads:** If a user reads data, any future reads by that same user will never return an older version of that data. They only ever see the data move forward in time.

## 4. Implementation Patterns (How to achieve consistency)

To build these consistency models, developers use specific architectural patterns:

* **Quorum Consensus:** A voting system used in distributed databases (like Cassandra). You define `N` (total replicas), `W` (write quorum: how many nodes must confirm a write), and `R` (read quorum: how many nodes must agree on a read). If `W + R > N`, you guarantee strong consistency because the read and write nodes will always overlap.
* **Two-Phase Commit (2PC):** A protocol for strong consistency across multiple different databases. A coordinator asks all databases to prepare for a transaction (Phase 1). If all agree, it tells them to commit (Phase 2). If one fails, the whole transaction is canceled.
* **Saga Pattern:** Used for eventual consistency in microservices. Instead of one massive transaction that locks multiple databases, a Saga breaks it down into a sequence of smaller, local transactions. If one step fails, the system runs "compensating transactions" to undo the previous steps.
* **Event Sourcing:** Instead of storing the current state of the data, the system stores a log of every single event (action) that has ever happened. The current state is calculated by replaying the events. This pairs well with eventual consistency.
