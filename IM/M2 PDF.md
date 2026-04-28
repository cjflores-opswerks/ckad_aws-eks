
# M2-1 Notes: What is an Incident? Incident Management & Its Importance

Source: 

## 1. Main Objective

This PDF explains what an **incident** is, how it differs from related concepts, and why structured incident management matters. It also introduces the full incident lifecycle, alert handling, severity classification, and escalation.

The main goal is to understand how teams detect, respond to, resolve, and learn from incidents.

---

## 2. What is an Incident?

An **incident** is any unplanned interruption or degradation in the quality of an IT service that impacts, or could impact, users, systems, or business operations.

The key idea is:

> An incident is about **impact**, not just abnormal behavior.

For example:

A database crash that causes users to see errors is an incident.

A short CPU spike with no user impact is not necessarily an incident.

An alert only becomes an incident when it affects users or has serious potential to affect users.

---

## 3. Related Concepts

The PDF explains a hierarchy of concepts:

**Event**  
Any observable occurrence in a system.

**Alert**  
A notification that something abnormal happened.

**Incident**  
A user-impacting issue or a system issue that can affect business operations.

**Problem**  
The underlying root cause of one or more incidents.

**Service Request**  
A user-initiated request, not a failure. For example, asking for access or requesting a configuration change.

---

## 4. What is Incident Management?

Incident management is the structured process of:

1. Detecting incidents
2. Responding to incidents
3. Resolving incidents
4. Learning from incidents

The goal is to restore service quickly and minimize business or customer impact.

---

## 5. Key Roles in Incident Management

**On-call Engineer**  
The first responder. Investigates alerts and starts initial response.

**Incident Commander**  
Coordinates the response and owns major decisions.

**Responders**  
Engineers who actively fix or mitigate the issue.

**Communications Lead**  
Updates stakeholders, customers, or internal teams.

**Scribe**  
Documents the timeline, actions taken, and decisions made.

---

## 6. Incident Lifecycle Process

The incident lifecycle follows this order:

### 1. Detection

The issue is discovered through monitoring, alerts, user reports, or engineer observation.

### 2. Alert Handling

The alert is reviewed to determine whether it is actionable and whether it indicates real user impact.

### 3. Triage

The team assesses scope, severity, affected services, and urgency.

### 4. Response & Declaration

If needed, the issue is formally declared as an incident. Roles are assigned and response begins.

### 5. Mitigation & Resolution

The team works to stop or reduce user impact, then restores normal service.

### 6. Recovery

Systems are monitored to make sure the fix worked and the service is stable.

### 7. Post-Incident Review

The team reviews what happened, why it happened, what went well, and what must improve.

---

## 7. Why Incident Management Matters

### Business Impact

Incident management helps:

- Prevent revenue loss from downtime
- Protect brand reputation
- Maintain customer trust
- Support regulatory compliance

### Technical Impact

It helps:

- Reduce MTTR, or Mean Time to Resolve
- Improve system reliability
- Support SLA and SLO compliance
- Improve automation and alerting

### Organizational Impact

It helps:

- Build operational maturity
- Encourage blameless culture
- Improve cross-team coordination
- Reduce on-call burnout

A major comparison from the PDF:

Without incident management, an alert may be ignored and cause a 20-minute unnoticed outage.

With incident management, an actionable alert can be mitigated in about 3 minutes.

---

## 8. Incident Management Architecture Process

The architecture follows this flow:

### 1. Monitoring & Observability

Systems collect metrics, logs, and traces.

### 2. Alerting Rules Engine

Rules check thresholds, SLO alerts, and anomaly detection.

### 3. Alert Processing

Alerts are deduplicated, correlated, and filtered to reduce noise.

### 4. Alert Routing

Alerts are sent to the correct service owner, team, or on-call engineer.

### 5. Notification System

Notifications are sent through pager, chat, SMS, or email.

### 6. Incident Management

An incident is created, a war room may be opened, and status is tracked.

### 7. Escalation Policies

If no one responds or the issue worsens, the alert escalates to secondary responders or management.

### 8. Resolution & Recovery

The team fixes or rolls back the issue and confirms the system is stable.

### 9. Post-Incident Review

The team identifies root cause, improves alerts, and prevents recurrence.

Important idea:

> Alert handling is the front door of incident management.

Poor alert handling causes missed incidents or alert fatigue.

---

## 9. Key Alert Handling Concepts

### Signal vs Noise

Good alerts are actionable, urgent, and tied to user impact. Bad alerts are noisy, frequent, and non-actionable.

### Deduplication

Many related alerts should become one incident ticket. For example, 100 server failures should not create 100 pages.

### Correlation

Related symptoms should be connected. For example, API errors and database latency may come from the same root issue.

### Routing

The alert must reach the right team, right person, at the right time.

### Escalation

If the alert is not acknowledged, it should automatically escalate to a secondary responder or management.

---

## 10. Severity and Classification

### SEV1 / P1 - Critical Outage

All users affected. Service unavailable. Requires immediate response and full team mobilization.

### SEV2 / P2 - Major Degradation

A significant percentage of users are affected. Core functionality is impaired. Requires fast high-priority response.

### SEV3 / P3 - Minor Issue

Small percentage of users affected. Workarounds exist. Usually handled during business hours.

### SEV4 / P4 - Low Priority

Minimal or cosmetic impact. Logged but not paged.

---

## 11. Key Takeaways from M2-1

Incidents are defined by **impact**, not just abnormal behavior.

Structured response is critical because clear roles and a defined lifecycle speed up resolution.

Alerts are the entry point into incident management, so they must be actionable and routed correctly.

Teams should measure MTTR, SLO compliance, and incident frequency to improve over time.

Every incident should produce learning through post-incident reviews.

---

# M2-2 Notes: Incident Response & Standard Severity Levels

Source: 

## 1. Main Objective

This PDF provides a practical framework for alert handling and incident response. It focuses on how responders should prepare, respond, learn, and classify severity.

The goal is to standardize how incidents are declared, triaged, and resolved.

---

## 2. Core Principles

### Principle 1: Distributed Ownership

Any engineer can declare an incident or raise its severity. The team should not wait for permission.

### Principle 2: Bias Toward Overreaction

If unsure between two severity levels, choose the higher one. It is easier to de-escalate later than to lose time.

### Principle 3: Action Over Precision

Restoring service is more important than perfectly diagnosing the problem early. Root cause analysis can come later.

---

## 3. Phase 1: Prepare

This phase happens before anything breaks.

### Alerting Standards

Alerts should be:

- Actionable
- Accurate
- Timely
- Connected to a runbook and owner

A good alert tells responders what is wrong, who owns it, and what to do next.

### On-Call Readiness

The on-call process should include:

- Primary and secondary rotations
- Clear escalation paths
- Acknowledge within SLA, such as 5 minutes
- Ownership until explicit handoff

### Runbooks

Runbooks should include:

- Step-by-step failure instructions
- How to verify the issue
- How to mitigate quickly
- When to escalate further

### Incident Tooling

Teams need standardized tools for:

- Incident tracking
- Communication channels such as Slack or Teams
- Internal and external status updates
- Cross-team coordination

---

## 4. Phase 2: Respond

The response process follows this flow:

### 1. Detect

Create visibility and begin coordinated response.

### 2. Triage

Assess the impact and determine urgency.

### 3. Mitigate

Stop or reduce user impact. Speed is more important than elegance.

### 4. Resolve

Restore normal and stable operation.

---

## 5. Key Decision Rules for Responders

When responding to incidents:

- If debating severity, choose the higher one.
- Escalate if the cause cannot be identified within 10-15 minutes for P1 or P2 incidents.
- Prioritize stopping impact instead of waiting for root cause.
- Communicate every 15-30 minutes during active P1 or P2 incidents.

---

## 6. Detect and Triage Process

### Detect Steps

1. Alert fires, user reports an issue, or engineer observes anomaly.
2. Create an incident record immediately if none exists.
3. Include title, time detected, affected services, and reporter.
4. Assign initial severity.
5. Create a dedicated communication channel.
6. Assign an Incident Commander if severity is P2 or higher.
7. Notify on-call, relevant teams, and leadership for P1.

### Triage Questions

Responders should ask:

- Is this customer-facing?
- How many users are impacted?
- Is the issue spreading or contained?
- Is there a workaround?

### Escalation Triggers

Escalate when:

- The cause cannot be identified within 10-15 minutes for P1 or P2.
- Impact is increasing.
- Critical systems are involved.

---

## 7. Mitigate and Resolve Process

### Mitigation Options

The PDF emphasizes:

> Speed over elegance.

Common mitigation actions include:

**Rollback**  
Revert a recent deployment or configuration change.

**Traffic Control**  
Use rate limiting, feature flags, or traffic redirection.

**Failover**  
Switch to backup systems or alternate regions.

**Graceful Degradation**  
Disable non-critical features or serve cached content.

**Restart / Reset**  
Restart services, jobs, or infrastructure components.

Important rule:

> Do not wait for root cause to act. Prefer reversible actions.

### Resolution Steps

1. Verify recovery by checking that metrics are normal and errors are resolved.
2. Remove temporary fixes carefully.
3. Confirm stability during a 15-60 minute post-fix observation window.
4. Close the incident record with timeline, actions, and final status.
5. Communicate resolution to stakeholders and schedule the post-incident review.

---

## 8. Phase 3: Learn

After the incident, the team performs a Post-Incident Review, especially for P1 incidents.

### PIR Deliverables

The review should include:

- Root cause analysis
- Timeline of events
- What worked and what did not
- Action items with owners and deadlines

### Focus Areas

The team should look for:

- Detection gaps
- Alert quality issues
- Response delays
- Coordination issues

### Remediation

Short-term fixes may include patches, improved alerts, and updated runbooks.

Long-term improvements may include architecture changes, reliability investments, and trend reviews across incidents.

---

## 9. Standard Severity Levels

### P1 - Critical

Complete or near-complete outage of critical functionality.

Examples:

- Entire site down
- Payments failing globally
- Authentication system unavailable

Response: immediate, full team mobilized.

### P2 - High

Major degradation with significant user impact.

Examples:

- Checkout failing intermittently
- API latency extremely high
- Mobile app crashing widely

Response: urgent, Incident Commander assigned, leadership notified.

### P3 - Medium

Moderate issue with limited impact.

Examples:

- Search results delayed
- Notifications not sending
- Minor UI feature broken

Response: scheduled team response within hours.

### P4 - Low

Minimal impact or internal issue.

Examples:

- Minor UI bug
- Logging issue
- Internal dashboard incorrect

Response: planned work in normal workflow.

---

## 10. Quick Decision Checklist

### When Alert Fires

- Check if an incident already exists.
- If not, create one immediately.
- Assign severity, erring high.
- Notify on-call and stakeholders.

### Within First 10 Minutes

- Assess full impact scope.
- Escalate if unclear or growing.
- Begin mitigation actions.

### During Incident

- Prioritize stopping user impact.
- Document all actions in real time.
- Communicate every 15-30 minutes for P1 or P2.

### Before Closing

- Confirm full recovery.
- Monitor for stability.
- Send resolution update to stakeholders.

---

## 11. Key Takeaways from M2-2

Do not wait for certainty before acting.

Incidents are a team responsibility, not only the on-call engineer’s responsibility.

Over-communication is better than silence.

Always choose the higher severity if unsure.

Preparation through alerts, runbooks, on-call rotations, and tooling reduces chaos.

Every incident should result in learning and follow-up action items.

---

# M2-3 Notes: KPIs of Incident Management

Source: 

## 1. Main Objective

This PDF explains the key performance indicators, or KPIs, used to measure incident management performance.

The goal is to build a measurable and continuously improving incident management capability.

The main KPI flow is:

> TTD → TTA → TTDiag → TTM → TTR

Each KPI builds on the previous one. Delays early in the process cascade downstream.

---

## 2. TTD - Time to Detect

### Definition

TTD measures the time from:

> Incident occurrence → First detection

It measures observability effectiveness, not human performance.

### Why It Matters

TTD shows how long customers are impacted before the team even knows there is a problem.

It is tied to:

- Monitoring coverage
- Signal quality
- Detection gaps
- Hidden failures

### Common Pitfalls

- Measuring from alert firing instead of actual degradation
- Missing silent failures where no alert is generated
- Alert suppression hiding detection delays

### Improvement Strategies

- Use symptom-based alerting based on golden signals.
- Add synthetic monitoring for user journeys.
- Use event correlation or AIOps for anomaly detection.
- Use distributed tracing for hidden failures.

Target:

> TTD ≤ 5 minutes

---

## 3. TTA - Time to Acknowledge

### Definition

TTA measures the time from:

> Detection → Human acknowledgment

It measures on-call responsiveness and alert quality.

### Why It Matters

If an alert is not acknowledged, the rest of the response process cannot start.

### Key Insight

Reducing alert noise improves TTA more than adding more people.

### Common Pitfalls

- Auto-acknowledgment making performance look better than it is
- Alerts routed to the wrong team
- Duplicate or flaky alerts

### Improvement Strategies

- Enforce high signal-to-noise alert policies.
- Use tiered escalation and ownership mapping.
- Track and remove flaky or duplicate alerts.
- Maintain reliable 24/7 on-call coverage.

Target:

> TTA ≤ 5 minutes

---

## 4. TTDiag - Time to Diagnose

### Definition

TTDiag measures the time from:

> Acknowledgment → Root cause identification

It measures system understandability and engineering maturity.

### Why It Matters

Diagnosis is often the longest cognitive phase of an incident.

It strongly predicts both time to mitigate and time to resolve.

### Influencing Factors

- Observability depth
- Logs, traces, and metrics correlation
- System complexity
- Quality of runbooks
- Knowledge sharing

### Improvement Strategies

- Invest in end-to-end observability tooling.
- Build and maintain diagnostic runbooks.
- Use blameless postmortems to improve future diagnosis.
- Create service dependency maps and automated root-cause hints.

Target:

> TTDiag ≤ 1 hour

---

## 5. TTM - Time to Mitigate

### Definition

TTM measures the time from:

> Diagnosis → Service restored via workaround

It measures operational agility, not perfection.

### Core Principle

> Mitigate first, fix later.

Customers care about restoration, not whether the team already understands the perfect root cause.

### Influencing Factors

- System resilience
- Failover and redundancy
- Rollback capability
- Decision-making authority
- Organizational risk tolerance

### Improvement Strategies

- Predefine mitigation playbooks.
- Enable feature flags, traffic shifting, and rollbacks.
- Run chaos engineering exercises.
- Reduce approval bottlenecks during active incidents.

Target:

> TTM ≤ 3 hours

---

## 6. TTR - Time to Resolve

### Definition

TTR measures the time from:

> Detection → Full resolution

It measures the full end-to-end incident lifecycle.

### Why It Matters

TTR represents the total business impact duration.

It is often an executive-level KPI.

### Dependencies

TTR depends on all earlier KPIs:

- TTD
- TTA
- TTDiag
- TTM

It also depends on change management speed, fix complexity, and release pipeline efficiency.

### Improvement Strategies

- Optimize earlier KPIs first.
- Parallelize mitigation and root-cause fix workflows.
- Improve CI/CD pipelines for faster fixes.
- Validate true stability before declaring resolution.

Target:

> TTR ≤ 6 hours

---

## 7. KPI Interdependency Process

The KPI process is:

### 1. TTD - Detect

Find the problem.

### 2. TTA - Acknowledge

A human accepts ownership.

### 3. TTDiag - Diagnose

The team identifies the cause.

### 4. TTM - Mitigate

The team reduces or stops user impact.

### 5. TTR - Resolve

The incident is fully resolved and closed.

Important idea:

> A delay early in the pipeline multiplies downstream impact.

---

## 8. Bottleneck Patterns

### Invisible Incidents

Symptom: High TTD, everything else appears fine.  
Root cause: Poor monitoring coverage.

### Alert Chaos

Symptom: Good TTD, poor TTA.  
Root cause: Alert fatigue and noise.

### Diagnosis Paralysis

Symptom: Strong detection, weak TTDiag.  
Root cause: Complexity and lack of observability.

### Slow Recovery

Symptom: Fast diagnosis, slow TTM.  
Root cause: Lack of operational readiness.

---

## 9. High-Performing vs Low-Performing Teams

### High-Performing Teams

Characteristics:

- Blameless culture
- Psychological safety
- Strong on-call ownership
- Continuous improvement mindset
- Heavy investment in observability

Behaviors:

- Treat alerts as product quality signals
- Regularly prune and tune alerts
- Conduct blameless postmortems
- Follow “you build it, you run it” ownership

### Low-Performing Teams

Common traits:

- Alert overload
- Reactive firefighting
- Knowledge silos
- Slow decision-making

---

## 10. KPI Target Summary

|KPI|Full Name|Target|
|---|---|---|
|TTD|Time to Detect|≤ 5 minutes|
|TTA|Time to Acknowledge|≤ 5 minutes|
|TTDiag|Time to Diagnose|≤ 1 hour|
|TTM|Time to Mitigate|≤ 3 hours|
|TTR|Time to Resolve|≤ 6 hours|

---

## 11. Key Takeaways from M2-3

Detection is the foundation because TTD drives the entire timeline.

Alert quality matters more than alert quantity.

Diagnosis is often the longest phase, so observability and runbooks are critical.

Mitigation should come before perfect root-cause analysis.

Elite incident response comes from optimizing the whole pipeline, not just one KPI.

---

# M2-4 Notes: SRE Fundamentals - SLO, SLA, SLI, Error Budget, and PQRST

Source: 

## 1. Main Objective

This PDF explains core SRE reliability concepts:

- SLI
- SLO
- SLA
- Error Budget
- PQRST incident communication

The goal is to help teams measure reliability, manage risk, and communicate incidents clearly.

---

## 2. SLI - Service Level Indicator

### Definition

An SLI is a metric that measures actual system behavior from the user’s perspective.

It answers:

> “How did we do?”

An SLI is not a goal. It is the raw measurement.

### Examples

**Request Success Rate**  
Percentage of successful HTTP responses.

**Latency**  
For example, p95 response time under 200 ms.

**Availability**  
Uptime over a 30-day window.

Important idea:

> SLIs are the foundation. Every SLO is built on one or more SLIs.

---

## 3. SLO - Service Level Objective

### Definition

An SLO sets a target for an SLI.

It answers:

> “What is acceptable?”

For example:

- 99.9% of requests must succeed over 30 days.
- p95 latency must be under 200 ms for 99% of requests.
- Error rate must stay below 0.1% monthly.

### Above SLO

If the service is above the SLO, performance is acceptable and teams can keep shipping.

### Below SLO

If the service is below the SLO, action is required immediately.

---

## 4. SLA - Service Level Agreement

### Definition

An SLA is a contractual promise to customers.

It answers:

> “What is contractually promised?”

An SLA usually includes:

- Reliability guarantee
- Penalties or credits
- Measurement window
- Exclusions or carve-outs

Example:

> “We guarantee 99.5% monthly availability, or you receive a 10% service credit.”

### SLO vs SLA

SLOs are usually stricter than SLAs.

Example:

- SLO: 99.9%
- SLA: 99.5%

The SLO acts as a safety buffer so the team can fix issues before the external SLA is breached.

---

## 5. Error Budget

### Definition

An error budget tells the team how much failure is allowed before the SLO is violated.

Formula:

> Error Budget = 100% - SLO%

Example:

If the SLO is 99.9%, then:

> Error Budget = 0.1%

For 1,000,000 requests in 30 days:

> 1,000,000 × 0.1% = 1,000 allowed failures

So the service can fail up to 1,000 requests in that month and still meet the SLO.

The PDF also gives an allowed downtime example:

> 99.9% SLO over 30 days = about 43.8 minutes of allowed downtime.

---

## 6. How Teams Use Error Budgets

### Budget Remaining

If the error budget is healthy, teams can ship features faster.

### Budget Exhausted

If the error budget is gone, teams should freeze risky changes.

### Small Incident

A small incident may be low urgency if the budget is healthy.

### Budget Nearly Gone

The same small incident can become critical if the budget is nearly exhausted.

This means urgency depends not only on the incident size, but also on how much reliability budget remains.

---

## 7. How SLI, SLO, SLA, and Error Budget Fit Together

The reliability pipeline is:

### 1. SLI measures behavior

Example: request success rate is 99.2%.

### 2. SLO sets thresholds

Example: request success must be 99.9%.

### 3. SLA defines external commitment

Example: customer contract guarantees 99.5%.

### 4. Error Budget guides decisions

Example: only 0.1% failure is allowed before the SLO is missed.

In practice:

1. SLIs measure real system behavior.
2. SLOs define acceptable performance.
3. SLOs are stricter than SLAs to create a buffer.
4. Error budgets quantify allowed deviation.
5. Teams use error budgets to guide deployments, risk-taking, and incident response.

---

## 8. PQRST Framework for Incident Communication

PQRST is used to make incident updates structured and clear.

During an incident, confusion slows response. PQRST helps everyone understand what is happening quickly.

### P - Provocation

Question:

> What triggered this?

Example:

A spike in HTTP 500 errors from the checkout service.

### Q - Quality

Question:

> What is actually broken?

Example:

Customers cannot complete purchases.

### R - Region

Question:

> Where is this happening?

Example:

Only us-west-2 is affected, or all mobile clients are affected.

### S - Severity

Question:

> How bad is it?

Example:

10% of requests are failing, or there is a 100% outage for new sessions.

### T - Time

Question:

> When did it start?

Example:

First alert at 09:32 PT, errors rising since 09:28.

---

## 9. PQRST Example

Bad update:

> “System is having issues.”

Better update:

> “500 errors at 12% in us-west-2 since 09:28, affecting checkout.”

Full PQRST example:

**P - Provocation**  
Spike in 500 errors from checkout service.

**Q - Quality**  
Customers cannot complete purchases.

**R - Region**  
All regions affected.

**S - Severity**  
8% failure rate, consuming 50% of monthly error budget in 1 hour.

**T - Time**  
Started at 09:28 PT following deployment.

---

## 10. Connecting SLOs and PQRST

SLOs and error budgets define urgency.

PQRST communicates urgency.

SLO questions include:

- Are we breaching an SLO?
- How fast are we burning the error budget?
- Is the SLA at risk?

PQRST helps communicate:

- Technical impact to engineers
- Business risk to leadership
- Time pressure to everyone

Together, SLOs and PQRST move the team from chaotic guessing to data-driven incident response.

---

## 11. Key Takeaways from M2-4

SLIs are raw signals. Without them, reliability is guesswork.

SLOs define the line between acceptable performance and action-required performance.

SLAs are contractual promises, so SLOs should be stricter to protect the SLA.

Error budgets make risk decisions objective.

PQRST turns vague updates into clear, structured, actionable communication.

SLO plus PQRST creates data-driven urgency and precise stakeholder communication.