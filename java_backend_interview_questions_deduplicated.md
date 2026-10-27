# Java Backend Interview Questions — Deduplicated Short Answers

> Consolidated from the screenshots in this conversation. Similar and repeated questions have been merged. Answers are intentionally short, conversational and interview-ready.

## How to use this guide

Read the answer aloud in your own words, then prepare one project example and one trade-off for important topics. For design questions, these are starting points, not complete architecture solutions.

## Core Java, JVM and OOP

### 1. What is fail-fast vs fail-safe iteration?

Fail-fast iterators usually throw ConcurrentModificationException when a collection is structurally changed during iteration. Concurrent collections often provide weakly consistent or snapshot iterators instead; “fail-safe” is an informal term.

### 2. How does ConcurrentHashMap work internally?

It uses concurrent reads and fine-grained coordination for updates, such as CAS and bin-level locking. Unlike a synchronized HashMap, unrelated keys can often be updated concurrently.

### 3. What happens when a thread pool queue is full?

If the pool cannot add workers or queue the task, its rejection policy runs. The default AbortPolicy throws RejectedExecutionException; alternatives include CallerRunsPolicy.

### 4. Why are mutable HashMap keys risky?

If fields used by hashCode or equals change after insertion, lookups may fail. Prefer immutable keys with stable equals and hashCode.

### 5. Explain SOLID principles.

Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation and Dependency Inversion. In practice, they help keep services focused, extensible and testable.

### 6. What is the Liskov Substitution Principle?

A subclass should work anywhere its parent is expected without breaking promised behavior. Inheritance is useful only when that “is-a” contract genuinely holds.

### 7. Optional.orElse() vs orElseGet()?

orElse evaluates its fallback eagerly; orElseGet invokes the supplier only when the Optional is empty. Use orElseGet for expensive fallback work.

### 8. What is Optional used for?

It represents a value that might be absent and makes that possibility explicit. It is especially useful for return values, not as a universal replacement for nullable fields.

### 9. What are the key Java 17 features?

Java 17 is an LTS release. Useful language features available by then include records, sealed classes, pattern matching for instanceof, switch expressions and text blocks.

### 10. What does var mean in Java? Can it be a generic type?

var lets the compiler infer a local variable’s static type. It is not a real type name and cannot be used as a generic type argument like List<var>.

### 11. What is an effectively final variable?

A local variable that is assigned once and never reassigned. Lambdas and inner classes can capture it even without the final keyword.

### 12. What is a Java record and how do you map query results to one?

A record is a concise immutable data carrier with generated accessors, constructor, equals and hashCode. Map query columns to its constructor through a projection or mapping layer; use findFirst() to get the first result.

### 13. What are checked exceptions and their trade-offs?

The compiler requires handling or declaring them. They make recoverable failures visible, but too many can clutter APIs and encourage meaningless catch blocks.

### 14. How do you reverse a string without built-in reverse methods?

Walk from the last character to the first and append to a StringBuilder. For full Unicode correctness, iterate code points rather than individual char values.

### 15. Can an interface contain private methods?

Yes, since Java 9. Private interface methods let default and static methods share internal logic.

### 16. What methods can an interface declare?

Abstract, default, static and private methods. Interface methods are public unless private, and fields are implicitly public static final.

### 17. What if two interfaces define the same default method?

The implementing class must override the conflicting method. It can explicitly call InterfaceName.super.method() to reuse one implementation.

### 18. Abstraction vs encapsulation?

Abstraction exposes what an object does; encapsulation protects how it does it. Interfaces give abstraction, while private fields and controlled methods provide encapsulation.

### 19. Inheritance vs polymorphism?

Inheritance reuses or specializes behavior through an is-a relationship. Polymorphism lets callers use one interface while different implementations behave differently.

### 20. What is a Singleton? Eager vs lazy?

Singleton provides one instance within a defined classloader. Eager initialization creates it when the class initializes; lazy initialization creates it on demand. An enum is often the simplest robust Java singleton.

### 21. What is the Builder pattern?

It constructs objects step by step, especially when many optional fields exist. It pairs well with immutable objects because the final object can be created once.

### 22. What is the Factory Method pattern?

It delegates object creation behind a method or abstraction. Callers ask for a type of service instead of directly coupling themselves to concrete constructors.

### 23. What is the difference between HashSet and LinkedHashSet?

Both keep unique elements. HashSet does not promise iteration order; LinkedHashSet maintains insertion order, usually with some extra memory cost.

### 24. How does HashMap resize and treeify?

When its load threshold is crossed, the table grows and entries are redistributed. Large collision chains may become balanced trees when capacity and bin thresholds are met.

### 25. How do you find unique characters in sorted order?

Count character frequencies, keep those with count one, and sort them. A TreeMap or frequency array plus sorted traversal works well.

### 26. What is BigDecimal and why use it for money?

Binary floating point can introduce rounding drift. BigDecimal represents decimal amounts precisely when constructed from strings or safe conversions; always choose rounding and scale rules explicitly.

### 27. Why does BigDecimal("2.0").equals(BigDecimal("2.00")) return false?

equals checks both numeric value and scale. compareTo returns zero because the numeric values are equal.

### 28. What are serialization and Serializable vs Externalizable?

Serialization converts object state into a transferable form. Serializable uses Java’s default/custom serialization hooks; Externalizable requires explicit readExternal and writeExternal logic.

### 29. What is JUnit vs Mockito, and mocking vs stubbing?

JUnit runs assertions and tests; Mockito creates test doubles. Stubbing defines a fake response, while mocking can also verify interactions.

### 30. How does garbage collection work? G1 vs ZGC?

GC reclaims unreachable heap objects. G1 balances throughput and predictable pauses using regions; ZGC targets very short pauses with more concurrent work. Tune only after measuring.

### 31. What are heap, stack and native memory?

The heap stores most objects, thread stacks hold call frames, and native memory includes direct buffers, metaspace and JVM internals. A container can run out of memory even when heap usage looks fine.

## Concurrency, Streams and Async Java

### 32. synchronized vs ReentrantLock?

Both protect critical sections. ReentrantLock adds timed/interruptible acquisition and multiple conditions; synchronized is simpler and automatically releases its monitor.

### 33. What does volatile guarantee?

Visibility of writes across threads and ordering constraints. It does not make compound operations such as count++ atomic.

### 34. volatile vs AtomicInteger?

volatile is good for publishing a value or flag. AtomicInteger provides atomic operations such as incrementAndGet using CAS.

### 35. What is a race condition?

When results depend on the unpredictable timing of concurrent operations. Fix it by controlling shared mutable state with locks, atomics or ownership.

### 36. Thread vs Runnable?

Thread represents an execution thread; Runnable describes work to execute. Prefer submitting tasks to executors instead of manually creating many threads.

### 37. What is the thread lifecycle?

A thread moves through NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING and TERMINATED states depending on scheduling and synchronization.

### 38. When do virtual threads help, and when not?

They help with many concurrent blocking I/O tasks. They do not speed up CPU-bound work or fix downstream capacity limits; monitor pinning and resource contention.

### 39. Which pool runs CompletableFuture.supplyAsync by default?

Usually ForkJoinPool.commonPool() when no executor is supplied. Blocking HTTP work can starve that shared pool, so pass a suitable executor or use an appropriate I/O model.

### 40. What are key CompletableFuture methods?

supplyAsync starts work; thenApply transforms; thenCompose chains dependent async work; thenCombine joins independent results; exceptionally/handle recover; allOf waits for multiple futures.

### 41. Sequential vs parallel streams?

Sequential streams process in one execution flow. Parallel streams split work across threads and help mainly for sufficiently large CPU-heavy, independent operations; measure before using them.

### 42. stream() vs flatMap()?

stream() creates a stream from a collection. flatMap() turns each element into a stream and flattens those streams into one.

### 43. What are functional interfaces, lambdas and method references?

A functional interface has one abstract method. Lambdas provide its implementation inline; method references are shorthand when an existing method fits.

### 44. Intermediate vs terminal stream operations?

Intermediate operations such as map and filter are lazy. Terminal operations such as collect, count and forEach trigger processing.

### 45. Why do @Async tasks start late and memory grow?

The executor may have too few workers and an oversized queue. Check active threads, queue depth and task duration; configure a bounded pool and rejection policy.

## Spring Boot, Web and Security

### 46. How does the Spring container work?

It scans or receives bean definitions, creates objects, injects dependencies, applies post-processors/proxies and manages lifecycle.

### 47. What is the Spring bean lifecycle?

Spring instantiates, injects dependencies, runs initialization callbacks, serves the bean and invokes destruction callbacks when the context closes.

### 48. What does @EnableAutoConfiguration do?

Spring Boot selects configuration based on the classpath, existing beans and properties. You can override defaults by declaring your own beans or configuration.

### 49. @Qualifier vs @Primary?

@Primary picks a default bean when multiple candidates exist. @Qualifier explicitly selects the desired candidate at an injection point.

### 50. IoC vs dependency injection?

Inversion of Control is the broad idea that a framework controls object creation and wiring. Dependency injection is one common way to implement it.

### 51. What are the types of dependency injection?

Constructor, setter and field injection. Constructor injection is usually preferred for required dependencies and easy testing.

### 52. What is a Spring profile?

A named environment configuration, such as dev or prod. It can activate specific beans and settings without changing application code.

### 53. PathVariable vs RequestParam?

PathVariable reads a value embedded in the URL path, such as /users/42. RequestParam reads query parameters, such as /users?page=2.

### 54. What are singleton, prototype, request and session scopes?

Singleton is one bean per Spring context; prototype creates on each lookup; request and session scopes tie beans to HTTP request and session lifetimes.

### 55. Why does a prototype injected into a singleton stay the same?

Injection normally happens when the singleton is created. Use ObjectProvider, scoped proxy or lookup methods to request fresh prototype instances.

### 56. Why can a singleton bean leak user data?

A singleton is shared across requests. Storing request-specific mutable state in its fields lets concurrent users overwrite or read each other’s data.

### 57. How does @Transactional work internally?

Usually a Spring AOP proxy opens or joins a transaction before the method and commits or rolls back afterward. Rollback defaults apply to unchecked exceptions unless configured.

### 58. Why does @Transactional self-invocation fail?

Calling this.method() bypasses the Spring proxy, so transactional advice does not run. Move the method to another bean or use an appropriate programmatic transaction.

### 59. How do you implement global exception handling?

Use @RestControllerAdvice with @ExceptionHandler methods to translate expected failures into consistent HTTP status codes and error responses.

### 60. What are lazy and eager JPA fetch types?

Lazy fetch loads related data when needed; eager fetch requests it immediately. Both can create unexpected queries, so choose fetch plans per use case.

### 61. Why can returning JPA entities cause infinite JSON recursion?

Bidirectional relationships can serialize parent to child and back forever. Return DTOs, or configure serialization carefully.

### 62. RestTemplate vs WebClient vs RestClient?

RestTemplate is older blocking API; RestClient is modern fluent blocking API; WebClient supports reactive, nonblocking I/O. Choose based on your application model.

### 63. How do microservices communicate?

Synchronously through HTTP/gRPC or asynchronously through Kafka/queues. Use timeouts, retries, idempotency and clear contracts.

### 64. What does an API Gateway do?

It is the client-facing entry point for routing, authentication, rate limiting and sometimes response aggregation. Keep business logic in services.

### 65. How does JWT authentication work?

An identity provider signs a token with claims and expiry. The API verifies signature, issuer, audience and expiry, then authorizes the request using its claims.

### 66. OAuth2 vs JWT?

OAuth 2.0 is an authorization framework; JWT is a token format. OAuth access tokens may be JWTs or opaque strings.

### 67. How does Spring Security work?

A security filter chain authenticates requests and enforces authorization rules. You configure the filter chain, identity sources and endpoint permissions.

### 68. How do you secure distributed microservices?

Use an identity provider, short-lived access tokens, validated audiences/scopes and service-to-service identity. Each service must enforce its own authorization.

### 69. How do you implement a JWT filter?

Prefer Spring Security OAuth2 Resource Server when possible. It validates bearer tokens and creates an authenticated security context without custom parsing code.

### 70. Authentication vs authorization?

Authentication asks who you are; authorization asks what you are allowed to do.

### 71. How do you version APIs without breaking old clients?

Use backward-compatible additive changes first. For breaking changes, introduce a versioned contract (path/header) and run a deprecation window.

### 72. Why do deployments cause 502 errors?

A pod may stop accepting or finish serving traffic before load balancers remove it. Use readiness probes, connection draining and graceful shutdown.

### 73. How can Spring Boot handle 100 concurrent users?

Requests are handled by server threads or reactive event loops depending on the stack. Capacity depends on CPU, thread pools, database connections and downstream latency; load-test the bottlenecks.

## Kafka and Messaging

### 74. Kafka vs RabbitMQ?

Kafka is a durable distributed event log with replay and high-throughput streams. RabbitMQ is a broker strong at flexible routing and work queues. Pick based on delivery and consumption needs.

### 75. How do Kafka partitions work?

Each topic is split into ordered partition logs. A record key typically determines its partition, and consumers in a group divide partitions among themselves.

### 76. How is ordering guaranteed in Kafka?

Ordering is guaranteed within a partition, not across an entire multi-partition topic. Send events for the same entity with the same key and handle retries carefully.

### 77. What happens when a consumer crashes mid-batch?

Records after the last committed offset can be read again after restart or rebalance. Commit only after successful processing and make handlers idempotent.

### 78. How do you build an idempotent Kafka consumer?

Give each business event a stable ID and store processed IDs with a unique constraint in the same transaction as the business update. On redelivery, skip already applied events.

### 79. What is a DLQ?

A separate topic or queue for records that cannot be processed after a controlled retry policy. Keep the error, original payload and metadata for investigation.

### 80. What are Kafka core components?

Producers write records to topics/partitions on brokers; consumers read them; consumer groups share work; offsets track positions; replicas provide durability.

### 81. Producer publishes but consumer sees nothing. How do you debug?

Check topic/cluster, ACLs, partitions, producer acknowledgments, consumer group, offsets, assignment, deserialization and lag. Verify with a console consumer.

### 82. Why is a consumer repeatedly removed from its group?

Slow processing can exceed max.poll.interval.ms; missed heartbeats, network problems or rebalances also cause it. Inspect group logs, poll duration and settings.

### 83. How do you handle a poison-pill message?

Catch and classify the failure, use bounded retries and route permanent failures to a DLQ. Commit/advance safely only after the recovery policy is applied.

### 84. What is producer idempotence? Does it deduplicate equal keys?

enable.idempotence prevents duplicate appends caused by producer retries using producer IDs and sequence numbers. It does not deduplicate two intentional sends or records with the same key.

### 85. What is consumer offset management?

Offsets identify the next position to read per partition/group. Auto-commit is simple but may lose processing guarantees; manual commits align progress with completed work.

### 86. What is consumer group rebalancing?

Kafka redistributes partition ownership when membership or partitions change. Processing may pause briefly, so consumers should handle revocation and duplicate delivery.

### 87. How do you sort out-of-order events in batches of 100?

Buffer 100 events, then sort by timestamp with a deterministic tie-breaker such as user ID. If global event-time ordering is required, add watermarks and a lateness policy.

## Database, SQL and JPA

### 88. First-level vs second-level Hibernate cache?

First-level cache is per persistence context and always present. Second-level cache is optional and can share cached entities across sessions.

### 89. What is the N+1 query problem?

One query loads parents, then one extra query per parent loads children. Fix with fetch joins, entity graphs, projections or batch fetching as appropriate.

### 90. How do you investigate a slow query?

Capture the SQL, parameters and execution plan; check indexes, row estimates, locks, scans and data volume. Change one thing and measure again.

### 91. How do you handle concurrent updates to the same row?

Use optimistic locking with a version column for normal contention, or pessimistic locking when necessary. Define retry and conflict behavior.

### 92. How do you rewrite nested subqueries with joins?

Identify the relationships and join predicates, then rewrite while preserving filtering and duplicate semantics. Compare execution plans; joins are not automatically faster.

### 93. Why is deep OFFSET pagination slow?

The database may scan and discard many earlier rows. Keyset/cursor pagination uses the last seen indexed sort key and scales better for deep sequential paging.

### 94. Why can two reads in one transaction return different values?

Under READ COMMITTED, another transaction may commit between reads. Isolation levels such as REPEATABLE READ change this behavior, depending on the database.

### 95. Why did deleting 10 million rows cause replica lag?

One huge transaction creates heavy logging and replication work. Delete in small batches, archive or drop old partitions where appropriate, and monitor lag.

### 96. How do you query across database-per-service boundaries?

Prefer APIs, events and read models rather than cross-service database joins. Build an aggregated projection for frequent cross-service queries.

### 97. How do you index and shard a food delivery database?

Index common filters such as restaurant_id, user_id, status and created_at. Shard high-volume entities by a stable key and plan for cross-shard queries and hotspots.

### 98. SQL: users who spent more than ₹10,000 in a chosen month last year?

Join orders to users, filter the date range, group by user and HAVING SUM(amount) > 10000. Use a half-open date range so an index on order date can work.

### 99. What are JPA projections?

They select only fields needed by the API, often into interfaces or DTOs. This reduces unnecessary entity loading and serialization.

## System Design and Distributed Systems

### 100. How would you design a payment system?

Start with payment intent, ledger, processor integration and status transitions. Use durable idempotency keys, database constraints, outbox events, reconciliation and audit logs.

### 101. How do you scale to one million transactions per hour?

That averages about 278 per second, though peaks matter more. Load-test, partition work, tune DB access, use queues where appropriate and design for failure/retries.

### 102. Saga vs two-phase commit (2PC)?

2PC coordinates a distributed atomic commit but can block and tightly couple participants. Saga uses local transactions and compensations with eventual consistency.

### 103. What is the Transactional Outbox pattern?

Write the business change and an outbox event in one DB transaction. A relay publishes the event later, avoiding a lost event between commit and publish.

### 104. How does distributed locking work?

A shared coordinator grants a lease or lock to one worker at a time. Use expirations and fencing tokens where stale lock holders could cause damage.

### 105. How do you design a Top K YouTube videos system?

Count views from events, aggregate by time window and maintain ranked candidates using heaps or sorted sets. Define freshness, deduplication and anti-abuse rules.

### 106. How does a social media feed scale?

Precompute follower feeds for ordinary accounts (fan-out on write), but fetch celebrity content at read time to avoid huge fan-out. Merge, rank and cache results.

### 107. How does real-time driver tracking work?

The driver sends frequent GPS updates; a location service indexes them and pushes updates to subscribed riders via WebSocket or similar connections.

### 108. How does a notification reach a phone quickly?

Publish an event, let a notification worker consume it, then use APNs/FCM, SMS or email. Retries and deduplication prevent repeated sends.

### 109. How would you build a URL shortener?

Generate a unique ID, encode it with Base62, store the destination, and redirect on lookup. Cache popular links and handle abuse, expiry and uniqueness.

### 110. How do you rate-limit 10,000 requests per second from one user?

Apply a token bucket or sliding-window policy at the gateway, keyed by user/client. For multiple servers, use atomic Redis operations or a dedicated distributed limiter.

### 111. How would you design a shared tagging system?

Store tags and entity-tag mappings with tenant/product scope, permissions and searchable indexes. Publish changes for downstream search and analytics.

### 112. What if one tag maps to ten million entities?

Avoid loading the whole set; paginate, shard/index mappings, cache counts and use asynchronous updates for expensive aggregations.

### 113. How would you design a shared colour-list app?

Model users, lists, colours and permissions. Expose CRUD and invite APIs; enforce owner/viewer access on every read/write and send share emails asynchronously.

### 114. How do you design a human-approval API?

Persist PENDING_REVIEW and return 202 plus request ID. Provide status polling/webhooks, a deadline policy and a durable workflow that resumes on approval.

### 115. How do you prevent two reviewers approving the same request?

Use a conditional update WHERE status = PENDING_REVIEW, or optimistic locking. Only one update should win; treat later attempts as conflicts.

### 116. How do you resume work reliably after approval?

Commit the approval and outbox event together, then let a worker process it. Make the downstream action idempotent in case the event is delivered again.

### 117. How do you audit approval decisions?

Write an append-only history of who changed what, when and why, alongside the current status. Protect access and retain it according to policy.

### 118. How do you handle duplicate payment requests?

Use a client-generated idempotency key scoped to the operation, persist its result with a unique constraint, and return the prior result on retry. A Redis-only check is not enough.

### 119. What happens if debit succeeds but credit fails?

Within one database, use an ACID transaction. Across services, use a saga with durable states, compensation where safe, and reconciliation; never assume every payment can simply be reversed.

### 120. How does CQRS help at scale?

Separate the write model from read-optimized projections when access patterns differ. It improves read flexibility but adds consistency and operational complexity.

### 121. How do you ensure a scheduled job runs once across three pods?

Use a shared scheduler, lease/lock or leader election, and make the job idempotent. A lock alone does not guarantee exactly-once side effects.

### 122. How do you ship a breaking API field rename?

First add the new field while retaining the old one, migrate clients, monitor usage, then deprecate and remove it in a controlled version.

### 123. How do you trace a request across six services?

Propagate trace context through HTTP and messaging; emit spans with OpenTelemetry and correlate trace IDs in logs.

### 124. What is a circuit breaker?

It stops calling an unhealthy dependency after failures cross a threshold, then probes recovery. Pair it with timeouts, bounded retries and fallback behavior.

### 125. What is the chance at least one of 50 independent calls fails if each succeeds 99.9%?

1 − 0.999^50 ≈ 4.88%. This assumes independent calls and that any single failure fails the whole request.

### 126. Why does Redis KEYS * stall traffic?

KEYS scans the keyspace and can block Redis’s event loop for a long time. Use incremental SCAN for operational iteration and avoid keyspace scans on request paths.

## Production Troubleshooting and Reliability

### 127. What are your first steps for OutOfMemoryError?

Check heap dumps, GC logs, memory charts, recent changes and allocation hotspots. Distinguish heap OOM from direct/native memory or container OOMKilled.

### 128. Why can a pod be OOMKilled with low heap usage?

Container memory includes direct buffers, metaspace, thread stacks, native allocations and page cache. Compare RSS and cgroup limits with JVM heap metrics.

### 129. Why are production timestamps off by 5.5 hours?

Usually UTC versus Asia/Kolkata conversion or inconsistent timezone configuration. Store instants in UTC and format in the requested zone at the boundary.

### 130. How do you debug p99 latency versus slowness for everyone?

Look at p50/p95/p99 and segment by endpoint, instance and dependency. Tail-only latency suggests contention, outliers or retries; broad latency suggests a shared bottleneck.

### 131. How do you isolate DB time from application time?

Use tracing and timers around queueing, connection acquisition, SQL execution, serialization and downstream calls. Do not guess from one overall latency number.

### 132. Why is a query slow despite an index?

The optimizer may choose a scan due to selectivity, stale statistics or plan changes; the index may not match predicates/order. Inspect EXPLAIN ANALYZE.

### 133. DB latency is normal but request latency is high. What next?

Check connection-pool wait, thread queueing, network, JSON serialization, cache misses, downstream calls and GC pauses.

### 134. Is connection-pool saturation a root cause?

Sometimes, but often it is a symptom of slow queries, leaked connections or too much concurrency. Measure active, idle and pending connections and query duration.

### 135. Are growing GC pauses the root cause?

They can be, but high allocation or memory pressure elsewhere may trigger them. Correlate GC logs with allocation rate, heap occupancy and latency.

### 136. What fails when every request reads one hot row?

The DB/cache or connection pool can become a hotspot. Cache safe read-only data, replicate or denormalize appropriately, and measure contention.

### 137. Why is the service slow only from 9 to 10 a.m.?

Look for predictable traffic peaks, batch jobs, cache expiry, deployments and autoscaling delays. Compare that hour with normal baselines.

### 138. Blue-green vs canary deployment?

Blue-green switches traffic between two full environments. Canary gradually sends a small fraction to the new version and checks metrics before expanding.

### 139. How do you implement graceful shutdown?

Stop accepting new work, mark the instance unready, drain requests, finish or safely checkpoint work, then close resources within the termination window.

### 140. How do you prevent a downstream outage from cascading?

Use strict timeouts, circuit breakers, bulkheads, bounded queues, retry budgets and backpressure. Degrade gracefully when possible.

## Coding, DSA and Machine Coding

### 141. How do you compute the mean of the last n streaming values?

Keep a fixed-size deque and running sum. On insertion, add the new value and subtract the oldest if the window exceeds n; average in O(1).

### 142. How do you exclude the k highest values from a sliding-window mean?

Maintain balanced ordered multisets or heaps with lazy deletion for the top k and remaining values, plus the sum of the remainder. Rebalance after each slide.

### 143. How do you find the top N rated movies connected to a given movie?

Traverse the connected component with BFS/DFS and keep a min-heap of the best N ratings. For dynamic edge additions, DSU helps maintain components.

### 144. How do you solve Minimum Window Substring?

Use a sliding window and frequency counts. Expand until all required characters are covered, then shrink to find the smallest valid window.

### 145. How do you merge two n-ary trees by key?

Recursively match children by key using a map. Prefer B’s value on collisions, keep A’s child order, and append unmatched B children.

### 146. How do you find the right view of a binary tree?

BFS level by level and record the last node in each level, or DFS right-first and take the first node at each depth.

### 147. How do you perform zigzag level-order traversal?

Use BFS by level and alternate whether values are appended left-to-right or right-to-left.

### 148. How do you solve House Robber IV?

Binary-search the minimum possible maximum selected value; greedily test whether at least k nonadjacent houses can be selected under that limit.

### 149. How do you solve minimum-cost travel passes?

Use dynamic programming over travel days: choose a 1-, 7- or 30-day pass and take the cheapest total covering all days.

### 150. How do you count nested objects in a JSON-like string?

Parse valid JSON and recursively count object nodes. If the interview input is only balanced braces, count opening braces, but that is not a full JSON parser.

### 151. How do you find the digital root of 2^n for huge n?

Use digital root 1 + ((2^n − 1) mod 9); compute 2^n mod 9 using its cycle or modular exponentiation, without constructing the huge number.

### 152. How do you design a BNPL machine-coding system?

Model inventory, users, orders, credit limits and dues. Keep purchase and repayment operations consistent, enforce limits, and test partial settlement, overdue orders and stock constraints.

### 153. How do you design a music player with repeat modes?

Model playback state and repeat policy separately; support play/next/previous and playlist boundaries. Replace long conditionals with State or Strategy where it improves clarity.

### 154. How do you implement Snake with wraparound?

Represent the body with a deque and occupied cells with a set. Update head/tail atomically per tick; wrap coordinates using modular arithmetic.

### 155. Deque vs LinkedList for a snake body?

Deque is the interface; ArrayDeque is often a better implementation than LinkedList for head/tail operations due to locality and lower allocation.

### 156. What if two game inputs arrive simultaneously?

Serialize state transitions on one game loop or protect state updates. Otherwise direction changes can race and cause invalid moves.

### 157. How do you find top N nested collections by total file size?

Compute sizes recursively with memoization, detect cycles if the structure is not a strict tree, and maintain a min-heap of N results.

### 158. How do you solve Stock Price Fluctuation (LeetCode 2034)?

Track latest timestamp and price by timestamp; use ordered multisets or heaps with lazy deletion to return current, min and max efficiently.

### 159. How do you implement All O’one Data Structure?

Use a doubly linked list of frequency buckets and a map from key to bucket. Move keys between adjacent buckets for O(1) increment, decrement, min and max.

### 160. How do you track the most popular tag in real time?

Maintain counts and a max-heap or ordered buckets; for O(1) updates and max lookup, use an All O’one-style bucket list.

### 161. How do you build an extensible LLD?

Define responsibilities and interfaces, keep state transitions explicit, use composition, validate edge cases and add tests before extending behavior.

## Behavioral and Project Discussion

### 162. How do you explain a project end to end?

Start with the problem, scale and constraints; explain architecture, your decisions, trade-offs, measurable impact and what you would improve.

### 163. How do you discuss ownership and delegation?

Explain the goal, how you divided responsibilities, kept interfaces clear, removed blockers and tracked delivery. Use one specific outcome.

### 164. How do you answer conflict-resolution questions?

Use STAR: situation, task, action and result. Show that you listened, used evidence, made a decision and followed through.

### 165. How do you handle ambiguity in system design?

Clarify users, functional and nonfunctional requirements, traffic, consistency and failure cases before drawing components.

### 166. How do you explain a design in an interview?

Walk through API contracts, data model, core flow, bottlenecks, failure recovery, observability and trade-offs; justify why each piece exists.

## Design Patterns Reference

### 167. When do you use Singleton?

When one coordinated instance per scope is appropriate, such as a configuration holder. Avoid using it as a shortcut for global mutable state.

### 168. When do you use Abstract Factory?

When you need families of related objects, such as cloud-specific storage and queue clients, without coupling callers to concrete classes.

### 169. When do you use Prototype?

When copying a configured object is cheaper or simpler than building it again.

### 170. When do you use Adapter?

When integrating an external API whose interface differs from the one your application expects.

### 171. When do you use Bridge?

When two dimensions vary independently, such as message type and delivery channel.

### 172. When do you use Composite?

When clients should treat one object and a group uniformly, such as files and folders.

### 173. When do you use Decorator?

When adding behavior such as caching or logging around an object without changing its core implementation.

### 174. When do you use Facade?

When exposing a simple interface over a complicated subsystem.

### 175. When do you use Flyweight?

When many objects can share immutable common data to save memory.

### 176. When do you use Proxy?

When access needs interception, lazy loading, security checks or remote delegation.

### 177. When do you use Observer?

When multiple subscribers need notification of a state change.

### 178. When do you use Strategy?

When interchangeable algorithms should be selected without large conditional blocks.

### 179. When do you use Command?

When an operation should be represented as an object for queueing, retry or undo.

### 180. When do you use State?

When an object’s valid actions and behavior depend on its current state, such as a player or order lifecycle.

### 181. When do you use Visitor?

When adding operations over a stable object hierarchy without changing each object class.

### 182. When do you use Memento?

When saving snapshots of state for undo or rollback without exposing internal details.

### 183. When do you use Iterator?

When traversing a collection without exposing its storage representation.

### 184. When do you use Mediator?

When many components need coordinated interaction without direct many-to-many dependencies.

### 185. When do you use Chain of Responsibility?

When a request passes through ordered handlers such as validation, authentication and routing.

### 186. When do you use Template Method?

When several algorithms share a fixed sequence of steps but customize selected steps.

---

**Total: 186 distinct questions and concise answers.**
