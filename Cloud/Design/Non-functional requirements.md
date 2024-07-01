## Low latency
1) caching - quick access of frequently requested data
2) database indexing - speeds up query responses
3) load balancer - efficiently distributes traffic to instances
4) **partitions in topics in Kafka** increases throughput but also INCREASES LATENCY. There is always tradedoff between throughput and latency as both are inversely proportional

## Highly available
It means minimal downtime and being operational most of the time to 99%
1) multiple load balancer with health checks and failover capabilities
2) database replication - implement multi-region db replication in case an entire region goes down and fails
3) MSA architecture
4) Kafka's leader/follower to replicate the leader replica in a different partition improves availability, in case that leader replica fails.

## Consistency
It is a tradeoff between availability and consistency. Consistency is seeing the updated data correctly.

![Screenshot 2024-07-01 152123](https://github.com/brian6484/CSKnowledge/assets/56388433/3a707d52-889b-4ca8-994c-f17e3b5c8d52)

1) We use quorum consensus. Since data is replicated at nodes, we want to ensure consistency of replication.
Here, n is number of replicas, w is no. of replicas that acknowledges that it has been written successfully and r is
the same concept for read.

If w=r=1, it is very fast read and write since we only need 1 acknowledgement from just 1 server. BTW w=1 doesnt mean we are writing to just one server. It just means We can just get **one** ACKS and that is done.

If w+r>n, normally n is 3 and w,r is 2 each, it ensures consistency.

2) versioning
3) vector locks

## Fault-tolerance
It is system's ability to operate normally, even when some of its components go down. Netflix is known for testing its fault-tolerance by intenionally shutting down its components to test its FA.
1) Circuit breaker pattern - literally like electrical Circuit breaker. If the components are detected to be failing, it prevents *cascading failures* by stopping requests to that particular
service that is failing
2) Retry mechanism - like Kafka's retry topic, can go 1 step to Exception topic for events that fail continuously
3) Fallback mechanism - provide better user experience during down time

## Reliability
It is NOT the same as Highly available. Reliability means system's ability to function **without failure** so minimising **number of failures** that system encounters. Highly available is
minimising **downtime duration** during a failure.
1) Redundancy - redudant db, server instances, etc
2) Fault tolerance - we can minimise failure if there is fault tolerance like retries, circuit breaker and fallbacks.

## Security
1) HTTPS
2) rate limiting - make a rate limit policy to prevent abuse, AWS WAF rules for example
3) Input validation - prevent injection attacks by validating input at the client side

## Monitoring and logging
1) Monitoring stack like Prometheus - track system metrics like latency, CPU load, etc
2) Dashboards like Grafana - visualise metrics collected by Prometheus
3) Alerting system - based on the metrics

This monitoring stack of Prometheus and Grafana can be connected to database, Kafka, and the business services. Specifically for Kafka, there is Kafka Exporter, that exports metrics
to Prometheus. **Place the Exporter to each broker**, indicating connections to each broker and collecting its metrics.

