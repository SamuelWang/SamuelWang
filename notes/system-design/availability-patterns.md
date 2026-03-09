# Availability Patterns

In modern software development, building a system that works under ideal conditions is only half the battle. The real challenge is ensuring the system remains operational and accessible when components inevitably fail. This concept is known as **availability**.

For web applications, achieving "high availability" means designing an architecture that expects failures and handles them gracefully, ensuring users experience minimal disruption.

## Understanding Availability: The "Nines"

Availability is traditionally measured as the percentage of time a system is fully operational. The fundamental formula is:

$$\text{Availability} = \frac{\text{Uptime}}{\text{Uptime} + \text{Downtime}}$$

In the tech industry, availability goals are often discussed in terms of "nines." When a cloud provider or service guarantees a certain percentage, it translates to a specific amount of allowed downtime.

| Availability | Colloquial Name | Allowed Downtime per Year | Allowed Downtime per Month |
| --- | --- | --- | --- |
| **99%** | Two nines | ~3.65 days | ~7.3 hours |
| **99.9%** | Three nines | ~8.76 hours | ~43.8 minutes |
| **99.99%** | Four nines | ~52.6 minutes | ~4.38 minutes |
| **99.999%** | Five nines | ~5.26 minutes | ~26 seconds |

While "five nines" (99.999%) is often the holy grail of system design, it is incredibly difficult and expensive to achieve. For most standard web applications, a target of three nines (99.9%) or four nines (99.99%) is a practical and reliable goal.

## How Architecture Impacts Availability

The way you connect the components of your web application directly impacts the overall availability of the entire system.

### 1. Components in Sequence (Series)

When a system requires multiple components to work together one after the other, they are operating in sequence. For example, a user's login request might need to pass through a Web Server **and** an Application Server **and** a Database. If any single component in this chain fails, the entire request fails.

When components are in sequence, the total system availability is the product of their individual availabilities:

$$A_{\text{total}} = A_1 \times A_2 \times \dots \times A_n$$

**The Impact:** Every time you add a new required component in sequence, your overall system availability *decreases*. If you have a Web Server and a Database, each with 99.9% availability (0.999), the total availability drops:


$$0.999 \times 0.999 = 0.998001$$

 (or 99.8%)

### 2. Components in Parallel (Redundancy)

To solve the problem of decreasing availability, architects use redundancy—connecting identical components in parallel. In this setup, a request can be handled by Server A **or** Server B. The system only fails if both servers go down at the exact same time.

To calculate parallel availability, you determine the probability of total failure and subtract it from 1:

$$A_{\text{total}} = 1 - ((1 - A_1) \times (1 - A_2) \times \dots \times (1 - A_n))$$

**The Impact:** Adding identical components in parallel significantly *increases* your overall availability. If you have two web servers running in parallel, both with 99.9% availability, the chance of both failing simultaneously is extremely low ($0.001 \times 0.001 = 0.000001$).


$$1 - 0.000001 = 0.999999$$


Your total system availability increases to an impressive 99.9999%.

## Essential Design Patterns for High Availability

To achieve the math shown above and protect against failures, developers rely on several proven architectural patterns.

### Load Balancing

This pattern distributes incoming network traffic across a group of backend servers. A load balancer sits in front of your servers and routes requests to healthy nodes. If one server crashes, the load balancer simply stops sending traffic to it, preventing any single point of failure.

### Redundancy and Replication

This involves having multiple instances of a component or keeping multiple copies of data in sync.

* **Active-Active:** Multiple identical servers or databases run simultaneously to handle requests.
* **Active-Passive (Failover):** A primary server handles all traffic while a secondary server sits on standby. If the primary crashes, traffic is automatically redirected to the passive backup.

### Circuit Breaker

Inspired by electrical switches, this pattern prevents a system from repeatedly trying to execute an operation that is likely to fail. If a downstream service is down, the circuit breaker "trips" (opens) after a set number of failures. New requests instantly return a fallback response rather than waiting for a timeout. After a cooldown, it allows a few test requests through to see if the service has recovered.

### Retry with Exponential Backoff

Temporary network glitches happen. Instead of failing immediately, the system automatically retries the request. To avoid overwhelming a struggling service, it uses "exponential backoff," increasing the wait time between each retry (e.g., waiting 1 second, then 2 seconds, then 4 seconds).

### Health Checks

A system cannot route around failures if it does not know they exist. Health checks involve regularly sending pings or test requests to services and databases to verify they are functioning. If a node fails, it is temporarily removed from the active pool.

### Bulkhead Pattern

Named after the partitioned sections of a ship's hull, this pattern isolates different parts of a system. Resources (like thread pools or memory) are allocated specifically to different components. If one component is overloaded, it exhausts only its own resources, keeping the rest of the application afloat.
