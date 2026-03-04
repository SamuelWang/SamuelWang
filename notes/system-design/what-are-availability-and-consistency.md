# What are Availability and Consistency?

In distributed software system design, **availability** and **consistency** are two foundational concepts that describe how a system handles data and responds to user requests. They are most famously linked by the CAP Theorem, which states that a distributed system can only guarantee two out of three characteristics: Consistency, Availability, and Partition Tolerance.

Here is a breakdown of what each term means and how they impact the systems you build.

## Consistency

Consistency means that every read request receives the most recent write, or an error.

In a consistent system, all nodes (servers or databases) see the exact same data at the exact same time. If a piece of data is updated on one node, that update must be replicated to all other nodes before any new read requests are answered.

* **The Goal:** Prevent users from seeing outdated or "stale" data.
* **Real-World Example:** A banking application. If you withdraw $100 from your account at an ATM, and immediately check your balance on your phone, the phone app must show the updated balance. It is better for the system to throw an error and deny the read request than to show you an incorrect amount of money.

## Availability

Availability means that every request receives a non-error response, regardless of the state of individual nodes in the system.

In a highly available system, the system remains operational 100% of the time. Even if some servers fail or lose connection to each other, the surviving servers will continue to accept reads and writes. However, there is no guarantee that the response contains the most recently updated data.

* **The Goal:** Keep the system running and responsive for users, no matter what.
* **Real-World Example:** A social media feed or an e-commerce product catalog. If someone leaves a comment on a post, it is okay if it takes a few extra seconds for that comment to appear for a user on the other side of the world. Keeping the website online and loading quickly is more important than immediate, perfect synchronization.

## The Trade-Off

Because distributed systems inevitably experience network partitions (where nodes lose connection to one another), system architects are forced to choose between consistency and availability during a failure:

| Strategy | What happens during a network failure? | Best Used For |
| --- | --- | --- |
| **CP (Consistency + Partition Tolerance)** | The system will cancel operations or return an error until the network is fixed to prevent conflicting data. | Financial transactions, billing systems, inventory management. |
| **AP (Availability + Partition Tolerance)** | The system continues to accept requests. Nodes might temporarily serve stale data, but they will eventually sync up later (called "eventual consistency"). | Messaging apps, social media, shopping carts, analytics. |
