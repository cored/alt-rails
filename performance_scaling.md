# Summary

It addresses typical scaling issues, implementation of horizontal scaling, necessary database changes, adoption of a service-oriented architecture, performance testing tools, and ensuring security alongside scaling.

# Motivation

This proposal aims to ensure that a Company’s infrastructure can effectively handle the anticipated load of 500K+ concurrent users on their Ruby on Rails API.

# Guide-level explanation

Typical Scaling Issues in a Ruby on Rails Application
Common issues like slow database queries, inefficient code, or lack of caching can hinder performance.
Limited CPU, memory, or network bandwidth can become bottlenecks under heavy load.
As user data grows, the database may need help to handle increasing read and write operations.

As user demand increases, optimizing the performance of a Ruby on Rails application to prevent slow response times, downtime, and a negative user experience is crucial. It is important to scale the application to handle concurrent requests, maintain database efficiency, and effectively use server resources.


## Implementing Horizontal Scaling in ROR:

Ruby on Rails supports various caching mechanisms, such as page caching, fragment caching, and HTTP caching.

To prevent the main application from getting overwhelmed by time-consuming tasks that don't require real-time processing, you can use tools like Sidekiq or Resque to delegate tasks to background jobs.

Delegate certain operations that take a long time, such as sending emails or generating reports, to background jobs so that users don't have to wait.

Most applications need background jobs for mailers, regular clean-ups, or any other time-consuming operation that doesn't require a user to be present.

You might already have a background worker set up.

If you do anything that takes more than a second to do inside a controller action, try moving it to a background worker instead. This could range from a user-facing operation like searching for data inside a large table to an API method that ingests extensive data.

## Typical DB Changes for Scaling

Database queries can become a performance bottleneck.

Proper indexing should be used to optimize queries, N+1 query problems should be avoided through monitoring, and ActiveRecord’s query optimization methods should be utilized.

First, identify the queries that take the most time to execute.

A helpful way to do this is to query the `pg_stat_statements` table, which contains statistics about all SQL statements executed on the server. This will provide information about the query, the number of times it was called, and its average run time.

Once you have identified the slow queries, analyze why they are slow and optimize them.

```

-- Query to identify the slowest queries
SELECT query,
		calls,
		total_time / 1000 AS total_time_ms,
		avg_time / 1000 AS avg_time_ms
FROM pg_stat_statements
ORDER BY total_time DESC
LIMIT 10;
```

You can also use `EXPLAIN` or `EXPLAIN ANALYZE` on a query to view its plan and execution details.

One of the most important things to look out for in the results is `Seq Scan`, which indicates that Postgres has to scan all the records sequentially to run the query.

If you encounter this, consider adding an index to the columns you filter to bypass the sequential scan.

```
-- Suppose you have a table named "your_table" and you want to optimize a query.
-- Here's a sample query that might result in a sequential scan:

EXPLAIN SELECT * FROM your_table WHERE your_column = 'some_value';

-- After running the EXPLAIN command, if you find that it performs a sequential scan,
-- you might want to consider adding an index to the column "your_column" to improve performance.

-- To create an index on "your_column", you can use the following SQL command:

CREATE INDEX idx_your_column ON your_table(your_column);

-- After creating the index, re-run the EXPLAIN command to verify that the query plan has improved.
-- If the index is successfully utilized, you should see an index scan instead of a sequential scan in the query plan.

```

Database changes for scaling may include implementing database sharding, where data is partitioned across multiple database instances based on a sharding key.

Additionally, read replicas can be configured to handle read-heavy operations, offloading the read load from the primary database.

## Migrating to a Service-Oriented Architecture (SOA)

Companies often cite seemingly harmless process problems, like longer lead times to add new features due to large amounts of code, extended code review time because of a vast codebase, introduction of bugs in unrelated areas due to a small change, and extended test suite run times due to complex code, as reasons for shifting to a service-oriented architecture. However, as applications expand, so does complexity, both accidental and essential.

Teams that fail to manage this complexity face multiple issues at different levels as their applications grow, and due to increasing data dependencies, tests become more challenging to maintain and run slowly.

While services seem like a good solution to this problem, if there is a need for proper decoupling between systems, even a well-defined API may not be enough.

Adding 30 lines of code in four files may require changes across multiple services due to poor decoupling, resulting in a much more complex and time-consuming process. This can lead to a decrease in developer confidence and increased time spent on code merging and reviewing, even though services were meant to make the process easier.

For instance, they may need to scale a particular application area independently to handle a growing workload, or they may have several distinct areas of the application, each of which changes independently.

Most of these areas are data-in and data-out, with no persistent state, and fit within a simple request/response lifecycle. They may also want to extract those areas into their applications because they expect the complexity to increase.

The first and third examples are reasonable and could be good reasons for extracting services. Isolated components may make the process easier, especially from the perspective of mocking and testing, as well as surface area. However, the argument that services are the only way to isolate complexity is invalid, as it can be accomplished without using distributed services.

Instead of using tools like VCR or WebMock and stubbing API responses, it is recommended to stub at the interface and return real data structures.

Although services may result in faster individual test suites, running all would likely result in similar speeds.

Moreover, confidence is often reduced unless a suite is added to test against multiple services. The key positive impact factor is that the team has agreed on an explicit interface that multiple areas can communicate.

By applying these principles to existing code, information can have a known set of inputs and outputs (POST bodies, JSON responses) against a minimal set of operations (API endpoints). This can be achieved by funneling HTTP requests into individual mediators of responsibility (classes and methods or modules and functions). However, this approach requires a level of rigor that many teams might need to be more accustomed to.

In frameworks like Rails, the easiest thing to do might be to modify a value in `session` or update a record directly without giving it much thought.

Over time, dependencies bleed into one another, resulting in a tangled mess.

Moving to a service-oriented architecture requires defining boundaries between the application’s sub-domains.

The definition of boundaries should happen as part of the monolith.

## Tools for Performance Testing

Regularly monitoring the application's performance using tools like New Relic or Scout is essential. This will help you identify bottlenecks and optimize the application accordingly.

Performance testing tools like Apache JMeter can simulate concurrent user traffic and assess the application's performance under load.

Profiling tools such as New Relic help identify performance bottlenecks, enabling optimization for scalability.


## Ensuring Security while Scaling

To ensure that scaling does not compromise security, implement security measures at every level of the system architecture.

Employ secure coding practices, such as input validation and parameterized queries, to mitigate vulnerabilities.

Utilize encryption for data at rest and in transit to enforce strong authentication and access control mechanisms.

Implement robust monitoring and incident response procedures to address any security threats promptly.
Additionally, conduct regular security assessments and penetration testing to identify and remediate vulnerabilities before they can be exploited, thereby maintaining a secure environment while scaling the system.

# Reference-level explanation:

Implementing horizontal scaling involves setting up a load balancer (e.g., Nginx, HAProxy) to distribute incoming requests to multiple ROR application instances deployed across different servers or containers.

Each instance should be stateless, allowing them to handle requests independently without relying on a shared local state.

The configuration for a simple load balancing setup using Nginx:

```
upstream rails_servers {
	server 10.0.0.1:3000;  # Replace with the IP address and port of your Rails server instance 1
	server 10.0.0.2:3000;  # Replace with the IP address and port of your Rails server instance 2
	server 10.0.0.3:3000;  # Replace with the IP address and port of your Rails server instance 3
	# Add more server instances as needed
}
```

```
server {
	listen 80;
	server_name your_domain.com;

	location / {
			proxy_pass http://rails_servers;
			proxy_set_header Host $host;
			proxy_set_header X-Real-IP $remote_addr;
			proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
			proxy_set_header X-Forwarded-Proto $scheme;
	}
}
```


Adopting a service-oriented architecture entails breaking down the monolithic ROR application into more minor services, each serving a specific business function. These microservices communicate via well-defined APIs, managed through an API gateway for centralized access control and routing. However, based on personal experience, moving into a distributed infrastructure without understanding the domain's boundaries will increase the complexity of development.

Yes, microservices might provide granular scalability in the short term, but the trade is a higher cognitive load to understand how the system works.

The alternative approach suggests a high-level approach to a system design using event modeling.

# Drawbacks:

Adopting horizontal scaling, service-oriented architecture, and implementing database changes can introduce development, deployment, and maintenance complexity.

Scaling infrastructure and employing performance testing tools may incur additional costs.

Introducing multiple services and components can increase the attack surface, requiring robust security measures or a dedicated team.

# Rationale and alternatives:

Event-driven architecture guides how to structure an application without prescribing any particular implementation.

It offers a flexible approach to scaling the various components that support business operations.

Creating adaptable code fosters the development of open-closed systems. This approach enables businesses to scale and adapt to changing needs efficiently.
Event-driven architecture simplifies the architecture and allows for greater flexibility

When an event is emitted, it determines which processors or functions will use it.

This allows for decoupling, meaning that apps using the event can change without requiring changes in the emitter. For example, when entering a room and generating an "entered room" event, the light turns on in response to the event rather than a command. In contrast, a command-based approach would involve flipping a light switch to turn on the light.

The event-first process supports repeated evaluation and processing, providing a "time machine."

It also offers horizontal scaling and a shared language with the business.

In this paradigm shift, we discard traditional messaging and send events without API coupling to a remote service. This enables processing events as reactions without the emitter calling on a specific function. The separation of writes and reads allows us to isolate each piece of infrastructure and scale it individually.

The value of events is that a sequence of related events represents behavior. A stream of events captures temporal behavior.

Events start as atomic and drive reactionary callbacks (functions). This raises many questions. Is ordering important? Do I want to ensure transactionality? How do I trust the execution? Security? Lineage? Where did the event come from? The critical realization for adopting event-first thinking is that an event represents a fact.

Something happened, and it is immutable, thus changing our domain model. The other consideration is that circumstances do not exist in isolation. Due to their very nature, an event tends to be part of a flow of information, a stream. A stream is an unbounded sequence of related events associated with an "eventKey," a key that ties many events together.

As the world evolves, new challenges must be innovative solutions.

Event-driven architecture reduces design complexity and increases operational efficiency. It will enable everyone to be responsible for the entire system and decouples the business infrastructure.

Event modeling is a collaborative workshop that uses simple tools to model business processes.



On the left side of the cheat sheet, the four possible building blocks are described:
Trigger: What ’triggers’ a use case? It can be a user via a UI or an external piece of software calling our public API. Or it can even be a robot, a.k .a. an automated process.

Describe it via a simple wireframe or the route of an HTTP endpoint. You can also involve designers to show the full-fledged design, but the main focus should remain on the information flow. So, for the start, it´s also okay to just use an empty white box with a name for the UI piece.

Command: Describes an intention to change the state of the system. Enrich it with relevant parameters.

Event: Describes a business fact that mutated the system's state and was saved to disk. This is the most crucial piece for understanding the system. Always find a name for it that explains what happened in the business context. Also, put all relevant information in it. The more realistic the data, the better.

View: Describes a query that reads, interprets, and curates previously produced data and provides it for a specific user interface. The queried data could also result in a report or another automated process that works with the data. In the case of an API, the views might be the data that supports a JSON response.

For example, a simple reading SQL query can be implemented in a table-based world. In a complex scenario, it could be a read model that aggregates events and persists the result somewhere.

Its primary goal is to share knowledge. Usually, these conversations happen, but they all occur with event modeling. This way, you can sort out any conflicts or discontinuities in any part of the domain.

At the same time, everyone involved is present and engaged.
If the developers need to understand the domain, they can model it.

The process starts by defining domain business events over a timeline. Each Event modeling session has a defined scope: to explore a specific business process of interest to the group.

During an event modeling session, we can gain clarity about the business processes that the system needs to support.

As we codify the timeline as a sequence of events, we create a single source of truth for what the system represents. This allows us to structure the system.

While adapting the structure, we ask participants what they want to see.
A chaotic mass of information can be overwhelming. They want to see who is doing what and when, step by step.

Events in this context not only transport data but also carry meaning.

Faq
