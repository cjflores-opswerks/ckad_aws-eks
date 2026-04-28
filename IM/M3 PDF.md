
# M3-1 Notes: System Health Monitoring

Source: 

## 1. Main Objective

This PDF explains **System Health Monitoring** for infrastructure in NOC/SOC environments. The goal is to keep infrastructure stable, detect incidents early, and respond quickly using defined KPIs, thresholds, and monitoring tools.

It focuses on **structural infrastructure health**, meaning the health of servers, networks, storage, and firewalls.

The presentation highlights:

- 7 core KPIs
- 3 monitoring tools
- 24/7 coverage

---

## 2. What is Structural System Health Monitoring?

Structural system health monitoring is the monitoring of infrastructure stability and availability.

It is different from security monitoring.

**Security monitoring** focuses on threats like malware, intrusion, or attacks.

**Structural monitoring** focuses on whether systems are healthy enough to keep services running.

It covers three main areas:

### Performance

This includes CPU, memory, and load average.

The goal is to make sure systems run within safe operating limits.

### Availability

This includes ping tests and uptime checks.

The goal is to confirm that servers and services are reachable.

### Security Hygiene

This includes firewall logs and network anomalies.

The goal is to catch infrastructure issues that could create security blind spots or outages.

---

## 3. How Structural Health Problems Become Incidents

The PDF shows that infrastructure signals can become incidents when they degrade.

Examples:

**CPU spikes** can lead to application outages.

**Packet loss** can lead to degraded services.

**Disk 100% full** can lead to security blind spots.

**Memory exhaustion** can cause failed logs or failed backups.

**Network latency** can lead to incident escalation.

The main idea is:

> Infrastructure issues often appear before users notice a service problem.

---

## 4. Core KPIs of Structural Monitoring

The 7 core KPIs are:

1. CPU utilization
2. Server ping
3. Load average
4. File system usage
5. Memory usage
6. Network monitoring
7. Firewall monitoring

Each KPI has warning, critical, and incident thresholds.

---

## 5. KPI Thresholds at a Glance

### CPU Utilization

- Warning: 70%
- Critical: 90%
- Incident: 100%

### Server Ping

- Warning: greater than 5% packet loss
- Critical: timeout
- Incident: latency greater than 200 ms

### Load Average

- Warning: greater than 50%
- Critical: greater than CPU core count
- Incident: sustained overload

### File System

- Warning: 80%
- Critical: 90%
- Incident: 95-100%

### Memory

- Warning: 75%
- Critical: 90%
- Incident: 95% plus swap usage

### Network

- Warning: greater than 2% packet loss
- Critical: greater than 200 ms latency
- Incident: greater than 85% bandwidth utilization

### Firewall

- Warning: denied connection spikes
- Critical: port scan
- Incident: flood

---

## 6. CPU Utilization Monitoring Process

CPU usage tells whether the server processor is overloaded.

The PDF shows CPU levels like this:

- 20%: lightly loaded
- 70%: moderately busy
- 95%: near saturation
- 100%: critical

### CPU Alert Trigger

An alert should trigger when:

> CPU is greater than 90% for 10 consecutive minutes.

### Possible Causes

High CPU can be caused by:

- Application overload
- High demand
- Inefficient queries or code
- Background batch jobs
- DDoS traffic
- Crypto mining malware

### Incident Response Process

1. Identify the top CPU-consuming processes using tools like `top` or Task Manager.
2. Check recent deployments or configuration changes.
3. Verify whether traffic levels are normal.
4. Restart or throttle problematic services.
5. Escalate if the issue continues.

---

## 7. Server Ping Monitoring Process

Server ping tests check reachability and availability.

They monitor:

- Packet success rate
- Packet loss percentage
- Latency
- Round-trip time

### Ping Alert Examples

A high-priority incident may be triggered by:

- Packet loss greater than 5%
- Ping timeout
- Server unreachable
- Latency greater than 200 ms

### Incident Response Process

1. Verify access through SSH or RDP.
2. Check network routes.
3. Confirm VM or cloud instance status.
4. Investigate firewall changes.
5. Escalate to network or infrastructure teams if needed.

Ping alerts usually create high-priority availability incidents because they indicate the server may be unreachable.

---

## 8. Load Average Monitoring Process

Load average shows how much work the system is handling compared with CPU capacity.

For an 8-core server, the PDF shows:

- 1-3: healthy
- 4: fully utilized
- greater than 4: overloaded

### Load Average Alert Trigger

An alert should trigger when:

> 5-minute load average is greater than CPU cores for 10 minutes.

### Possible Causes

High load average can be caused by:

- CPU saturation
- Disk I/O bottlenecks
- Application deadlocks
- Database query congestion

### Incident Response Process

1. Compare load average with CPU utilization.
2. Check disk I/O statistics.
3. Identify processes stuck in wait states.
4. Scale infrastructure if the load is legitimate.

---

## 9. File System Usage Monitoring Process

File system monitoring checks disk usage.

The PDF gives these thresholds:

- Safe: less than 80%
- Warning: 80-90%
- Critical: 90-95%
- Incident: 95-100%

Important directories to watch closely:

- `/var`
- `/tmp`
- Database volumes

### Why File System Usage Matters

If disk usage gets too high:

- Logs may stop writing.
- Applications may crash.
- Databases may fail.
- Security visibility may be lost.

### Incident Response Process

1. Find the largest disk users.
2. Remove unnecessary archives.
3. Rotate or compress logs.
4. Expand storage if needed.
5. Prevent recurrence by fixing log retention or growth patterns.

---

## 10. Memory Utilization Monitoring Process

Memory monitoring checks whether the system is running out of usable memory.

Thresholds:

- Normal: less than 75%
- Warning: greater than 75%
- Critical: greater than 90%
- Incident: greater than 95% plus swap usage

The PDF emphasizes that **swap usage is often more important than raw memory percentage**.

### Memory Alert Trigger

An alert should trigger when:

> Memory is greater than 95% and swap usage is increasing.

### Possible Causes

High memory usage can be caused by:

- Memory leaks
- High concurrent processes
- Large in-memory caches
- Malicious workloads

### Incident Response Process

1. Identify processes consuming too much memory.
2. Restart memory-leaking services.
3. Clear unnecessary caches.
4. Scale resources or increase RAM.

---

## 11. Network Monitoring Process

Network monitoring checks whether systems can communicate reliably.

Key metrics:

- Packet loss
- Internal latency
- Interface errors
- Bandwidth utilization

Thresholds:

- Packet loss: greater than 2-5%
- Latency: greater than 200 ms
- Interface errors: spikes detected
- Bandwidth utilization: greater than 85%

### Incident Response Process

1. Verify network devices.
2. Run traceroute.
3. Check bandwidth congestion.
4. Check interface errors.
5. Escalate to network engineering.

---

## 12. Firewall Monitoring Process

Firewall monitoring checks security and connectivity events.

Key signals:

- Large number of denied connections
- Unexpected open or exposed ports
- Suspicious source IP ranges
- Connection flooding attempts
- Spike in blocked traffic
- Unauthorized port access

Example alert:

> More than 1000 denied connections from a single IP in 1 minute may indicate DDoS or port scanning.

### Incident Response Process

1. Identify the source IP pattern.
2. Block or rate-limit offending IPs.
3. Check whether services are exposed unexpectedly.
4. Escalate to security if needed.

---

## 13. Sample Monitoring Tools

### Grafana

Best for infrastructure visualization and alerting.

Best KPIs:

- CPU
- Memory
- Load
- Disk
- Network

Example workflow:

> Metrics → Alert → Notify → Investigate

### Amazon CloudWatch

Best for AWS-native infrastructure monitoring.

Best KPIs:

- EC2 CPU
- Memory
- Network
- Disk I/O
- Load balancer health

Example workflow:

> High CPU → Auto-scale EC2 instances

### AppDynamics

Best for application performance monitoring and root cause tracing.

Best KPIs:

- Server performance
- Application latency
- Database
- Transactions

Example workflow:

> Detect → Trace transaction → Identify bottleneck

---

## 14. Key Takeaways from M3-1

Structural health is the foundation of availability.

CPU, memory, load, disk, network, and firewall metrics are early warning signs before users notice problems.

Well-defined thresholds help teams act before outages occur.

Sustained breaches matter more than short spikes.

The right tool should match the monitoring layer:

- Grafana for visualization
- CloudWatch for AWS infrastructure
- AppDynamics for application tracing

Health signals can also be security signals because DDoS, crypto mining, and data exfiltration may first appear as infrastructure anomalies.

Incident response must be documented, practiced, and supported by runbooks.

---

# M3-2 Notes: Application Health Monitoring

Source: 

## 1. Main Objective

This PDF explains **Application Health Monitoring**, which focuses on real-time performance, runtime health, alerting workflows, tracing, and monitoring tools.

The goal is to detect, diagnose, and resolve application incidents faster while reducing downtime and protecting user experience.

The PDF focuses on:

- Early detection
- Smart alerting
- Cross-service tracing
- Monitoring tools such as Azure Monitor, Dynatrace, and Grafana

---

## 2. What is Application Health Monitoring?

Application Health Monitoring checks whether an application is functioning correctly.

It combines:

### Performance Telemetry

Metrics such as:

- Latency
- Throughput
- Error rates
- Resource utilization

### Availability Checks

Checks that confirm whether endpoints, services, and application functions are reachable.

### Runtime Health Indicators

Signals such as:

- Memory usage
- Garbage collection
- Thread behavior
- Dependency status
- Infrastructure health

The general flow is:

> Telemetry Signals → Alerting Systems → Incident Management → Remediation

---

## 3. Application Performance Metrics

Common metrics include:

- Latency or response time
- Throughput, or requests per second
- Error rates
- Transaction durations
- Resource utilization
- Queue lengths
- Dependency latency, such as database or API latency

---

## 4. Metric Changes and Possible Incidents

The PDF connects common metric changes to possible incidents:

|Metric Change|Possible Incident|
|---|---|
|Increased response time|Database slowdown or network latency|
|Spike in error rate|API failure or code defect|
|Reduced throughput|Thread pool exhaustion|
|Transaction duration spike|Downstream service degradation|

The important idea is that application metrics are often the first sign that something is breaking.

---

## 5. How Performance Metrics Feed Alerting Workflows

The PDF gives four alerting scenarios.

### Threshold Alert

Example:

> If p95 response time is greater than 1 second for 5 minutes, trigger an incident.

This means latency stayed too high long enough to be considered real user impact.

### Error Rate Alert

Example:

> If error rate is greater than 5% over 2 minutes, page the on-call engineer.

This indicates users are experiencing failures.

### Rate-of-Change Alert

Example:

> If latency increases more than 200% within 3 minutes, raise a high-priority alert.

This detects fast degradation before a fixed threshold is reached.

### SLO-Based Alert

Example:

> If error budget burn rate is greater than threshold, trigger an SRE incident.

This is based on whether the service is consuming its allowed failure budget too quickly.

---

## 6. What Alerts Drive

Application alerts can trigger:

- PagerDuty or Opsgenie pages
- Incident ticket creation
- Slack or Teams alerts
- Automated remediation

The key point is that alerts should lead to action, not just notification.

---

## 7. Application Health Indicators

Application health can be:

- Healthy
- Degraded
- Unhealthy

Health indicators include:

### HTTP Health Endpoints

Detect whether the application process is alive.

### Port Checks

Detect whether the service is reachable.

### Synthetic Transactions

Detect whether key user workflows function correctly.

### Dependency Checks

Detect database or API health.

### Runtime Metrics

Detect memory, garbage collection, and thread issues.

---

## 8. Kubernetes Health Endpoints

The PDF lists three common Kubernetes endpoints:

### `/health`

Shows overall operational status.

### `/ready`

Shows whether the application can accept traffic.

### `/liveness`

Shows whether the process is alive.

These endpoints are important because they help Kubernetes decide whether to send traffic, restart a container, or mark a service as unhealthy.

---

## 9. Health Signal Examples

|Situation|Health Signal|
|---|---|
|Application crash|Health endpoint unreachable|
|DB connection exhaustion|Health degraded|
|Memory leak|Health gradually degrading|
|External API outage|Partial service health|

This shows that health monitoring is not just “up or down.” A service can be partially degraded.

---

## 10. Response Time and End-User Experience Monitoring

Total response time is made up of:

> App Processing + Database Time + External API Calls + Network Latency

The PDF gives latency examples:

- 250 ms: normal
- 600 ms: minor degradation
- 1,000 ms: incident threshold
- 2,300 ms: critical incident

### End-User Experience Metrics

Important frontend/user metrics include:

- Largest Contentful Paint, or LCP: less than 2.5 seconds
- First Input Delay, or FID: less than 100 ms
- Page Load Time: less than 3 seconds

Example alert:

> If page load time is greater than 4 seconds for 10% of users, create a performance incident.

---

## 11. Transaction and Cross-Application Tracing

Tracing follows a request across services to find where time is being spent.

Example checkout request trace:

- API Gateway: 20 ms
- Order Service: 40 ms
- Payment Service: 3700 ms
- Inventory Service: 250 ms

Total latency is about 4 seconds, and the Payment Service is the root cause because it takes 3.7 seconds.

The request path shown is:

> API Gateway → Auth Service → Order Service → Inventory Service → Payment Gateway

### Why Tracing Matters

Without tracing, teams may only know that checkout is slow.

With tracing, teams can see exactly which service caused the delay.

---

## 12. Port and URL Monitoring

Port and URL monitoring checks whether application endpoints are reachable and responding correctly.

Checks include:

- TCP port check, such as port 443 open
- HTTP response status, such as 200
- Response time less than 2 seconds
- Content check, such as response contains “OK”

Example endpoint:

> GET `https://api.example.com/health`

Example alert:

> If health endpoint failure is greater than 3 checks, page on-call immediately.

This detects:

- Application crashes
- DNS issues
- Load balancer misconfiguration

---

## 13. Garbage Collection Monitoring

Garbage collection, or GC, monitoring applies to runtimes such as Java JVM, .NET CLR, and Go.

Metrics include:

- GC pause
- Heap usage percentage
- GC frequency per minute
- Allocation rate MB/s

Example alert:

> If GC pause is greater than 2 seconds and heap is greater than 85%, trigger a memory incident.

Excessive GC can cause:

- Latency spikes
- Application freezes
- Memory leaks
- Crashes

---

## 14. Sample Monitoring Tools

### Azure Monitor

Used for Microsoft cloud-native observability.

Capabilities:

- Distributed tracing
- Dependency tracking
- Request latency tracking
- Failure rate tracking
- Availability tests
- Endpoint checks
- Dynamic thresholds
- Action groups
- Application Insights integration

### Dynatrace

Used for AI-driven observability.

Capabilities:

- Auto-instrumentation
- Smartscape topology
- PurePath distributed tracing
- Automated health scores
- Davis AI root-cause correlation
- Alert storm suppression

### Grafana

Used for open observability and visualization.

Capabilities:

- Integrates with Prometheus, Loki, and Elasticsearch
- Dashboards for latency, error rate, and throughput
- SRE war room visualization
- Multi-source alert correlation
- p95 and p99 latency panels

---

## 15. Impact of Application Health Monitoring

The PDF shows that monitoring can significantly reduce incident response times.

Examples:

- MTTD improves from 45 minutes to 5 minutes.
- MTTR improves from 120 minutes to 25 minutes.
- Escalation time improves from 30 minutes to 5 minutes.
- RCA duration improves from 180 minutes to 30 minutes.

It summarizes the impact as:

- 9x faster MTTD
- 5x faster MTTR
- 6x less RCA time

---

## 16. Key Takeaways from M3-2

Performance metrics are early indicators of incidents.

Application monitoring has two dimensions:

1. Performance: How fast is it?
2. Health: Is it up and healthy?

Percentiles such as p95 and p99 are better than averages because averages hide bad user experiences.

Distributed tracing helps identify root cause quickly in microservice environments.

Good alert quality reduces noise and alert fatigue.

Tools like Azure Monitor, Dynatrace, and Grafana help automate detection, correlation, and response.

---

# M3-3 Notes: Application Functionality Monitoring

Source: 

## 1. Main Objective

This PDF explains **Application Functionality Monitoring**.

The goal is to make sure the application’s business logic and service interactions behave as expected.

This is different from infrastructure monitoring because a server can be healthy while the actual application workflow is broken.

The two key monitoring metrics are:

1. Application flow
2. API response

---

## 2. What is Application Functionality Monitoring?

Application functionality monitoring checks whether the application actually performs its intended business operations correctly.

Infrastructure monitoring may show:

- CPU is normal
- Memory is normal
- Network is healthy

But functionality monitoring asks:

- Can users log in?
- Can users check out?
- Can payments process?
- Can data be submitted successfully?

This matters because many production incidents happen even when infrastructure metrics appear healthy.

---

## 3. Application Flow Monitoring

Application flow monitoring measures end-to-end business transactions.

It validates that the full sequence of steps completes correctly.

A typical workflow might be:

> Start → Auth Process → DB Write → Notify → Complete

Examples of business workflows:

### User Login Flow

> Auth → Session creation → Profile load

### Order Checkout Process

> Cart → Payment → Inventory → Confirm

### Payment Processing

> Gateway → Authorization → Settlement

### Data Submission Pipeline

> Validate → Transform → Store → Notify

---

## 4. Why Application Flow Monitoring Matters

The PDF emphasizes:

> Infrastructure health does not always equal application health.

Examples:

|Scenario|Infrastructure Health|Application Flow|
|---|---|---|
|Payment gateway failure|Servers healthy|Checkout fails|
|Logic bug in deployment|CPU and memory normal|Order processing stops|
|Database schema mismatch|Database running|API transactions fail|

Flow monitoring detects:

- Broken business logic
- Partial transaction failures
- Dependency failures
- Deployment regressions
- Misconfigurations

---

## 5. Application Flow Alert Handling

Alert triggers include:

- Workflow success rate drops below threshold
- Specific workflow step fails repeatedly
- Transaction latency exceeds baseline
- Critical business transaction fails

Example alert:

> Checkout success rate less than 95% for 5 minutes  
> Severity: Critical  
> Impact: Customers cannot complete purchases

### Operations Team Actions

1. Identify which business capability is broken.
2. Determine which service or dependency failed.
3. Escalate to the appropriate service owner.

---

## 6. Error Code Severity Mapping

The PDF maps error patterns to severity:

|Error Pattern|Severity|
|---|---|
|Minor increase in 4xx|Low|
|Sustained 5xx errors|High|
|Critical dependency error|Critical|
|Auth failures across services|High|

Common HTTP error groups include:

- 4xx client errors
- 5xx server errors
- Timeouts
- Specific application errors

---

## 7. API Response Monitoring

API response monitoring checks the performance, reliability, and health of service endpoints.

It measures:

- Response latency
- Success vs failure status
- Throughput
- Error responses
- Timeouts

It evaluates whether endpoints:

- Respond correctly
- Respond within acceptable time
- Maintain reliability under load

APIs are important because they power:

- Web apps
- Mobile apps
- Microservices
- Third-party integrations

---

## 8. API Cascade Risk

The PDF warns that API failures can cascade quickly in distributed systems.

A single failing endpoint can affect:

- Web applications
- Mobile clients
- Microservices
- Third-party integrations

This is why API monitoring is treated as a first line of defense.

---

## 9. Baseline API Latency Examples

The PDF gives example p95 latency by endpoint:

- `/login`: 200 ms
- `/checkout`: 400 ms
- `/search`: 300 ms
- `/profile`: 180 ms
- `/payment`: 350 ms

These baselines help detect when an endpoint is unusually slow.

---

## 10. Common API Problems Detected

The PDF lists common causes of API issues:

- Backend slowdowns
- Database query issues
- External dependencies
- Thread exhaustion
- Deployment defects

---

## 11. API Latency and Failure Rate

### Percentile Latency

The PDF shows:

- P95 latency: 320 ms
- P99 latency: 900 ms

Important idea:

> Percentile monitoring reveals tail latency issues hidden by averages.

P99 matters because it shows the slowest user experiences.

### Failure Rate Formula

> Failure Rate = Failed Requests / Total Requests

### Failure Rate Interpretation

|Failure Rate|Interpretation|
|---|---|
|Less than 1%|Normal|
|1-3%|Warning|
|Greater than 5%|Critical|
|Greater than 10%|Major Outage|

---

## 12. API Anomaly Detection

Anomaly detection identifies unexpected deviations without relying only on static thresholds.

Techniques include:

### Statistical Baselines

Uses standard deviation, seasonal baselines, and moving averages.

### Machine Learning

Uses multi-metric anomaly detection, hidden correlation analysis, and early degradation signals.

### Trend Analysis

Detects gradual degradation before total failure occurs.

Example from the PDF:

> Latency spike in off-peak hours, 250% above baseline.

---

## 13. Anomaly Escalation Process

The anomaly escalation process is:

1. Flag unusual behavior.
2. Correlate across services.
3. Generate alert for investigation.
4. Declare and mitigate incident.

This process helps teams catch unknown or unexpected failures earlier.

---

## 14. Monitoring Tools

### Dynatrace

Best for AI-driven observability.

Capabilities:

- Automatic instrumentation
- PurePath distributed tracing
- Davis AI root cause analysis
- Smartscape topology mapping
- Automatic health scoring

### Amazon CloudWatch

Best for AWS-native monitoring.

Capabilities:

- Native metrics, logs, and events
- EC2, ECS, Lambda, and RDS monitoring
- SNS notifications
- Lambda triggers
- Auto-remediation integration

### Splunk

Best for observability and analytics.

Capabilities:

- Log monitoring
- Error detection
- Metrics tracking
- Distributed tracing
- Real-time dashboards
- ML-based anomaly detection

---

## 15. Dynatrace Workflow

Dynatrace can monitor:

- Slow transactions
- Service latency
- Code-level bottlenecks
- Database query delays

Example PurePath trace:

> User → API Gateway → Order Service → Payment Service → Database

If the Payment Service is slow, Dynatrace can automatically create a “Problem” incident and link the root cause to that service.

---

## 16. CloudWatch Workflow

CloudWatch collects AWS metrics, logs, and events.

Example workflow:

> EC2 CPUUtilization greater than 85% for 5 minutes → SNS → Lambda auto-scales EC2

This shows how monitoring can trigger automated remediation.

---

## 17. Splunk Workflow

Splunk supports:

- Log monitoring
- Metrics monitoring
- Distributed tracing
- Real-time dashboards
- Anomaly detection

It helps reduce MTTR by correlating logs and metrics so teams can detect, analyze, and respond faster.

---

## 18. Key Takeaways from M3-3

Functional monitoring is not the same as infrastructure monitoring.

Healthy servers do not always mean healthy applications.

Application flow monitoring is business-critical because it maps directly to user-facing operations like checkout and payments.

API monitoring is the first line of defense because APIs connect web apps, mobile apps, microservices, and third-party systems.

Anomaly detection catches unknown failures that static thresholds may miss.

Functional alerts should be treated as high severity because they directly affect customers.

---

# M3-4 Notes: Database Monitoring

Source: 

## 1. Main Objective

This PDF explains **Database Monitoring** for performance, health, and incident response.

The goal is to help engineering and operations teams proactively monitor database systems, detect early failure signals, and respond effectively to incidents while minimizing customer impact and protecting data integrity.

The guide focuses on:

- What database signals to monitor
- Alert thresholds
- Incident response playbooks
- Monitoring tools used in production

---

## 2. Why Database Monitoring Matters

Database monitoring is important because:

- Database incidents can cause customer-facing outages.
- Degraded database performance affects users directly.
- Undetected replication lag or errors can create data integrity risks.
- Proactive monitoring prevents reactive firefighting.

Effective database monitoring focuses on four areas:

1. Performance
2. Health
3. Capacity
4. Error signals

---

## 3. Critical Database Monitoring Elements

The PDF lists 7 critical database monitoring elements:

1. Replication lag
2. Open connections
3. IOPS
4. Long-running queries
5. Throughput
6. Space usage
7. Errors

Together, these create the baseline for database observability.

---

## 4. Replication Lag Monitoring

### What It Measures

Replication lag measures the delay between:

> Transaction commit on primary database → Application on replica database

It can be measured in:

- Seconds
- Transactions
- Log sequence number differences

### Root Causes

Replication lag can be caused by:

- Disk I/O bottlenecks
- Network latency between nodes
- Long-running transactions
- Replica CPU saturation
- Write-heavy workloads

### Alert Thresholds

|Lag Duration|Severity|
|---|---|
|Less than 5 seconds|Normal|
|5-30 seconds|Warning|
|Greater than 30-60 seconds|Critical|
|Minutes or more|Potential replication failure|

### Incident Response Process

1. Check whether the replica is receiving logs or changes.
2. Check network latency between primary and replica.
3. Check disk I/O and CPU on the replica.
4. Identify long-running transactions.
5. Reduce write pressure or scale the replica if needed.
6. Escalate if lag reaches minutes or data freshness is at risk.

---

## 5. Open Connections Monitoring

Open connections measure active client sessions on the database server.

### Thresholds

- Less than 60%: normal
- 60-80%: warning
- Greater than 80%: critical
- Greater than 95%: outage risk

### Why It Matters

Too many open connections can exhaust the database and prevent new users or services from connecting.

### Incident Response Process

1. Identify top connection sources.
2. Check query performance.
3. Investigate connection pool configuration.
4. Kill runaway sessions if needed.
5. Scale the database instance if the issue is sustained.

---

## 6. IOPS Monitoring

IOPS means input/output operations per second.

It measures database read and write activity on the storage subsystem.

### Storage Utilization Thresholds

- Less than 60%: normal
- 60-80%: warning
- Greater than 80%: degradation risk
- Greater than 90%: critical

### Why It Matters

High IOPS or disk latency can slow queries, replication, and transactions.

### Incident Response Process

1. Check disk latency metrics.
2. Identify high I/O queries.
3. Verify whether backup or maintenance jobs are running.
4. Increase provisioned IOPS if needed.
5. Optimize queries and add indexes.

---

## 7. Long-Running Query Monitoring

### Why It Matters

Long-running or expensive queries can:

- Block other transactions
- Exhaust CPU or memory
- Increase lock contention
- Degrade the entire database

### Root Causes

Long-running queries may be caused by:

- Missing indexes
- Inefficient joins
- Large result sets
- Poor query plans
- Lock contention

### Query Duration Thresholds

- Less than 1 second: normal
- 1-10 seconds: moderate
- 10-30 seconds: warning
- Minutes or more: critical

### Incident Response Process

1. Identify the query fingerprint.
2. Review the execution plan.
3. Check for lock contention.
4. Kill the query if it is impacting the system.
5. Escalate to database engineering.

---

## 8. Throughput Monitoring

Throughput measures transactions and queries processed per second.

### Throughput Patterns

**Sudden spike**  
Could indicate traffic surge or runaway process.

**Sudden drop**  
Could indicate service degradation or outage.

**Gradual rise**  
Could indicate capacity pressure building over time.

### Incident Response Process

1. Compare throughput to normal baselines.
2. Check whether traffic changed unexpectedly.
3. Investigate application behavior or runaway processes.
4. Check database saturation indicators such as CPU, IOPS, and connections.
5. Scale or optimize if sustained.

---

## 9. Space Usage Monitoring

Space usage tracks storage utilization of volumes and tablespaces.

### Alert Thresholds

- Less than 70%: OK
- 70-85%: warning
- 85-90%: critical
- Greater than 95%: immediate action required

### Root Causes

Space growth may be caused by:

- Unarchived transaction logs
- Large bulk inserts
- Uncontrolled temporary table growth

### Incident Response Process

1. Identify which volume, table, or tablespace is growing.
2. Check transaction logs.
3. Archive or remove unnecessary data if safe.
4. Clean temporary tables if appropriate.
5. Expand storage.
6. Add prevention, such as log rotation or data retention rules.

---

## 10. Error Monitoring

Error monitoring tracks database-level error events, including:

- Connection failures
- Query failures
- Replication errors
- Disk errors

Alerts trigger on:

- Error rate spikes
- Specific critical error codes
- Repeated failure patterns

### Error Response Process

1. Review database logs.
2. Identify the error pattern.
3. Correlate the error with deployments or configuration changes.
4. Validate database availability.
5. Escalate if the errors affect users or data integrity.

---

## 11. Health Monitoring

Database health monitoring includes connection health and infrastructure health.

### Connection Errors

Normal condition:

- Very low error rate

Abnormal condition:

- Sudden spike in failures

Response:

1. Verify database availability.
2. Check authentication.
3. Check network connectivity.

### VM / Infrastructure Health

Normal condition:

- CPU less than 70%
- Stable memory

Abnormal condition:

- CPU saturation
- Memory exhaustion
- Disk spikes

Response:

1. Scale the VM.
2. Restart the instance if appropriate.
3. Investigate memory leaks or resource saturation.

---

## 12. Sample Monitoring Tools

### Oracle Enterprise Manager

Best for deep Oracle database diagnostics.

Capabilities:

- Real-time database performance monitoring
- Query performance analysis
- Storage monitoring
- Replication status tracking

Best used for:

- Deep Oracle database diagnostics
- Performance tuning
- Capacity planning

### Splunk

Best for log analysis and observability.

Capabilities:

- Database log ingestion
- Query performance analysis
- Error pattern detection
- Incident correlation

Best used for:

- Error analysis
- Root cause investigation
- Cross-system correlation

### Amazon CloudWatch

Best for AWS cloud-native database monitoring.

Capabilities:

- Metrics for RDS and Aurora
- Storage and IOPS monitoring
- Replication lag tracking
- Alarm-based alerting through SNS

Best used for:

- Infrastructure-level DB monitoring
- Cloud-native alerting

---

## 13. Tool Alert Workflows

### Oracle Enterprise Manager Workflow

1. Metric threshold is triggered.
2. Alert is generated in the OEM console.
3. Incident ticket is created.
4. DBA investigates the query or resource issue.

### Splunk Workflow

1. Log anomaly is detected.
2. Alert rule is triggered.
3. Incident is escalated to responders.
4. Root cause investigation begins.

### Amazon CloudWatch Workflow

1. Metric threshold is exceeded.
2. CloudWatch alarm triggers.
3. Notification is sent through SNS.
4. Incident is created in the ITSM system.

---

## 14. Key Takeaways from M3-4

Database monitoring should cover 7 critical signals:

- Replication lag
- Open connections
- IOPS
- Query performance
- Throughput
- Space usage
- Errors

Threshold-based alerting helps detect degradation before it becomes an outage.

Fast response requires runbooks for each metric.

Each database incident should follow a process:

> Identify → Investigate → Remediate → Escalate

Use the right tool for the right signal:

- Oracle Enterprise Manager for deep diagnostics
- Splunk for log correlation
- CloudWatch for cloud-native database monitoring

Full database observability requires both performance monitoring and health monitoring.