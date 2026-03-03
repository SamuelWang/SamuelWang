# What are Latency and Throughput?

Latency and throughput are two of the most critical performance metrics in software system design. While they are related, they measure fundamentally different things about how a system handles data and user requests.

Here is a breakdown of what they mean and how they apply to the systems you build.

## **1. Latency (The "How Fast")**

Latency is the **time it takes for a single unit of data to travel from its source to its destination.** In web applications, this is often thought of as the response time: the delay between a user clicking a button and seeing the result.

* **What it measures:** Time.
* **Common units:** Milliseconds (ms).
* **The Analogy:** Think of driving a car from your house to the grocery store. The 10 minutes it takes you to get there is the latency.
* **Web Context:** If a user makes an API request to fetch user profile data, the 200ms it takes for the server to process the query, retrieve it from the database, and send the JSON back to the browser is the latency. Low latency means a snappy, responsive user experience.

## **2. Throughput (The "How Much")**

Throughput is the **amount of data or number of requests a system can process successfully within a specific timeframe.** It measures the overall capacity of your system.

* **What it measures:** Volume over time.
* **Common units:** Requests per second (RPS), Transactions per second (TPS), or Megabytes per second (MB/s).
* **The Analogy:** Think of a highway. If latency is how fast a single car travels, throughput is the number of lanes on the highway. A wider highway (more lanes) allows more cars to pass through per hour, even if the speed limit (latency) stays exactly the same.
* **Web Context:** If your web server can successfully process 5,000 API requests every second without crashing or dropping connections, your throughput is 5,000 RPS. High throughput means your application can handle heavy traffic and scale well under load.

## **The Relationship (and The Trade-offs)**

It is important to look at both metrics together, as optimizing for one can sometimes impact the other.

* **Low Latency, Low Throughput:** A system that responds instantly but can only handle one user at a time. (Like a private toll road with only one lane).
* **High Latency, High Throughput:** A system that processes massive amounts of data, but takes a long time to deliver any single piece of it. (Like loading a massive database backup onto a truck and driving it across the country. The total data moved per day is huge, but the time for the first byte to arrive is very slow).

In modern web applications, the goal is usually to find a balance: keeping latency low enough so users don't get frustrated, while keeping throughput high enough to serve a growing user base.
