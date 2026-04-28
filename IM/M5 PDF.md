
I see the uploaded PDFs are **M5-1 through M5-4**, so I kept the notes in that order and clearly labeled each transition.

# Notes for M5-1: Basic Risk Awareness

Source: 

## 1. Main Topic

**Basic Risk Awareness** is about understanding, identifying, and managing risks in operations. Risk awareness helps teams recognize problems early, respond faster, reduce downtime, and prevent small issues from becoming major incidents.

## 2. What Risk Means

**Risk** is the possibility that something will go wrong and negatively affect systems, services, or customers.

Examples of risks:

- **System failure:** A database crashes, causing application downtime.
- **Data loss:** Backups fail silently, leaving no valid restore option.
- **Service degradation:** API latency spikes, making transactions slow.
- **Security breach:** Unauthorized access creates suspicious alerts.

## 3. Types of Risk Encountered

### Technical Risk

Technical risk comes from systems, infrastructure, software, or security weaknesses.

Examples:

- Memory leak causing gradual system failure.
- Misconfigured cloud infrastructure exposing services.
- Single point of failure in a critical database.
- Unpatched vulnerability leading to exploitation.

How it is detected:

- Monitoring alerts
- Logs
- System metrics

### Operational Risk

Operational risk comes from people, processes, communication, or dependencies.

Examples:

- On-call engineer misses an alert because of alert fatigue.
- Poor runbooks slow down incident response.
- Third-party API dependency unexpectedly goes down.
- Miscommunication during handoff delays escalation.

How it shows up:

- Delayed response
- Confusion
- Preventable escalation

## 4. Why Risk Awareness Matters

Risk awareness improves operations in three major ways:

### Better Prioritization

Teams can separate:

- Low-risk noise
- Medium-risk warning signs
- High-risk critical incidents

This prevents wasting time on low-impact alerts while serious issues are developing.

### Faster Response

Risk-aware teams escalate sooner when necessary and avoid unnecessary investigation on low-impact issues. This helps reduce **MTTR**, or Mean Time to Recovery/Resolution.

### Prevent Escalation

Early signals can reveal bigger problems forming, such as:

- A slow query becoming a database outage.
- A retry storm becoming system overload.

## 5. Consequences of Poor Risk Awareness

Poor risk awareness affects operations, business, and teams.

### Operational Consequences

- Missed alerts
- Slower incident response
- Increased downtime

### Business Consequences

- Revenue loss
- Customer dissatisfaction
- SLA breaches

### Team Consequences

- Burnout from alert overload
- Confusion
- Reactive operations instead of proactive operations

## 6. Risk Formula Framework

The PDF uses this formula:

**Risk = Likelihood × Impact**

### Likelihood

Likelihood asks: **How likely is this to happen?**

High likelihood examples:

- Known recurring issue
- System already unstable
- Errors increasing over time

Low likelihood example:

- Rare edge-case bug

### Impact

Impact asks: **How bad is it if it happens?**

Higher or critical impact examples:

- Affects customers
- Affects revenue or critical functions
- Affects a large number of users

Low impact example:

- Internal reporting delay

## 7. Process: Applying the Risk Formula

When an alert appears, the process is:

1. Identify the alert or issue.
2. Ask how likely it is to happen or worsen.
3. Ask how severe the impact would be.
4. Combine likelihood and impact.
5. Decide whether to act immediately, monitor closely, or defer.

Examples from the PDF:

- **Payment API error rate increase:** High likelihood and critical impact, so it needs immediate escalation.
- **CPU spike on app server:** Medium likelihood and medium impact, so it should be investigated closely.
- **Backup job failed:** Medium likelihood but critical impact. This is a hidden high-risk alert because the damage may appear later.
- **Disk 70% in non-production:** Low likelihood and low impact, so it can be scheduled for later.

Important note: **Hidden high-risk alerts are often underestimated because their impact is delayed, not immediate.**

## 8. How the Framework Improves Decision-Making

During alert triage:

- Assess likelihood and impact.
- Prioritize logically instead of emotionally.

During incidents:

- Focus on critical systems first.
- Justify escalation.
- Communicate risk clearly.

Under pressure:

- Avoid panic.
- Avoid tunnel vision.
- Make consistent team-wide decisions.

Result:

- Faster MTTR
- Fewer escalations
- Greater operational consistency

## 9. Key Takeaways from M5-1

- Technical risk can break systems through memory leaks, misconfigurations, and vulnerabilities.
- Operational risk slows recovery through alert fatigue, poor runbooks, and miscommunication.
- Every alert is a signal of risk, but not every alert deserves the same level of attention.
- Use **Likelihood × Impact** to decide whether to act immediately, monitor, or defer.
- Risk awareness supports proactive operations.
- Risk awareness is the foundation of reliable and resilient operations.

---

# Notes for M5-2: Risk Classification — Immediate vs. Potential Risk

Source: 

## 1. Main Topic

This PDF explains how to classify alerts as either **Immediate Risk** or **Potential Risk**. The goal is to help teams triage faster, respond appropriately, and avoid both underreaction and overreaction.

## 2. Objective

The framework aims to:

- Define clear thresholds for risk classification.
- Separate active or imminent threats from future risks.
- Guide triage decisions using a repeatable process.
- Reduce MTTD and MTTR by removing classification ambiguity.
- Prevent escalation by catching potential risks early.

## 3. Immediate Risk

### Definition

An **Immediate Risk** is any alert condition that shows active or imminent impact to production systems, users, security, or data. It requires urgent action within minutes to prevent or limit damage.

### Characteristics

Immediate risk usually has:

- Impact happening now or within minutes.
- Active outage, degradation, or SLA breach.
- Multiple users, services, or regions affected.
- Urgency signals such as P1/P2 pages, high error rates, or health check failures.

### Required Response

The process for immediate risk is:

1. Respond immediately within **0–5 minutes**.
2. Begin mitigation before full diagnosis.
3. Escalate if needed.
4. Prioritize stabilization.

## 4. Potential Risk

### Definition

A **Potential Risk** is an alert condition that shows a trend, anomaly, or vulnerability that could cause future impact, but no active or imminent user-facing damage is happening yet.

### Characteristics

Potential risk usually has:

- Impact that may happen hours or days later.
- No current outage.
- System still functioning within acceptable thresholds.
- Risk that depends on progression or additional triggers.
- Enough time to plan, investigate, and prevent escalation.

### Required Response

The process for potential risk is:

1. Acknowledge within SLA, usually **15–60 minutes**.
2. Investigate the issue.
3. Create a mitigation plan.
4. Track the issue through a ticket.
5. Schedule remediation.

## 5. Scenarios and Examples

### Scenario 1: API Error Rate Spike

Classification: **Immediate Risk**

Alert:

- Error rate jumps from 0.5% to 35% across all API endpoints.

Reason:

- Active user impact.
- Large blast radius.
- Likely ongoing outage.

Response:

- Roll back.
- Fail over.
- Isolate the faulty service.

### Scenario 2: Gradual Memory Increase

Classification: **Potential Risk**

Alert:

- Memory usage increases from 40% to 75% over 6 hours.

Reason:

- No current failure.
- Trend suggests future exhaustion.
- Time exists before impact.

Response:

- Investigate within hours.
- Plan restart, patch, or scaling.

### Scenario 3: Database CPU at 100%

Classification: **Immediate Risk**

Alert:

- Primary database CPU is maxed.
- Query latency increases 10x.

Reason:

- Active degradation.
- Likely cascading failures.
- Core dependency is stressed.

Response:

- Throttle traffic.
- Kill expensive queries.
- Scale or fail over.

### Scenario 4: Disk Usage at 85%

Classification: **Potential Risk**

Alert:

- Disk usage is steadily increasing.
- Projected to reach 100% in about 12 hours.

Reason:

- No current outage.
- Predictable future failure.
- Enough time to fix.

Response:

- Same-day cleanup.
- Increase storage.
- Adjust retention.

### Scenario 5: Multiple Failed Login Attempts

Classification: **Potential Risk**

Alert:

- 500 failed login attempts over 10 minutes from a single IP.
- No successful breach.

Reason:

- No confirmed compromise yet.
- Could escalate into brute force.
- Security signal exists, but no confirmed impact yet.

Response:

- Block IP.
- Enable rate limiting.
- Monitor for escalation.

### Scenario 6: Health Check Failing in One Region

Classification: **Immediate Risk**

Alert:

- Health checks failing for one service region.
- Traffic is still routed globally.

Reason:

- Partial outage is occurring.
- Risk of traffic spillover.
- Redundancy is being consumed.

Response:

- Remove the region from rotation.
- Investigate root cause immediately.

### Scenario 7: Elevated Latency Within SLA

Classification: **Potential Risk**

Alert:

- Latency increased from 80ms to 180ms.
- SLA threshold is 300ms.

Reason:

- No SLA breach yet.
- Could indicate a degradation trend.
- Still within acceptable bounds.

Response:

- Monitor and investigate.
- Optimize before the SLA threshold is crossed.

## 6. Process: 3-Step Triage Framework

Use this process to classify alerts quickly:

1. **Is impact happening now?**
    - Yes → Immediate Risk
    - No → Go to step 2
2. **Will impact occur within minutes?**
    - Yes → Immediate Risk
    - No → Go to step 3
3. **Is this a trend or warning signal?**
    - Yes → Potential Risk

Rule of thumb:  
**If users or systems are being impacted now or within the next few minutes, classify it as Immediate Risk. If not, classify it as Potential Risk.**

## 7. Comparison Summary

|Dimension|Immediate Risk|Potential Risk|
|---|---|---|
|Timeframe|Now or within minutes|Hours to days|
|Current Impact|Active or imminent|None yet|
|Urgency|Interrupt-driven/page|Scheduled response|
|Primary Focus|Mitigation and stabilization|Prevention and investigation|
|Delay Tolerance|Low; minutes matter|Moderate; planned response|
|Response SLA|0–5 minutes|15–60 minutes|

## 8. Key Takeaways from M5-2

- Immediate risks require speed because impact is active or minutes away.
- Potential risks should not be ignored because they can become immediate risks.
- Use the 3-step triage framework to classify alerts consistently.
- Context defines blast radius.
- Track trends, not just thresholds.
- Immediate risks should be paged and escalated.
- Potential risks should be ticketed and scheduled.

---

# Notes for M5-3: Response vs. Recovery

Source: 

## 1. Main Topic

This PDF explains the difference between **Response** and **Recovery** in incident management. These are separate phases, and both are necessary for operational resilience.

## 2. Objective

The presentation aims to:

- Define Response and Recovery clearly.
- Identify the goals and timelines of each phase.
- Use real-world incidents to explain the difference.
- Give teams shared language for incident handling.

## 3. Response

### Definition

**Response** is the immediate set of actions taken after an alert is triggered to detect, assess, contain, and stabilize an incident.

### Characteristics

- Timeframe: minutes to hours
- Primary goal: control the incident and stop it from worsening
- Focus: detect, assess, contain, stabilize
- Audience: on-call engineers and incident commanders

### Response Goals

There are four main response goals:

#### 1. Rapid Detection and Triage

Confirm that the alert is a real incident. Assess severity and scope within minutes.

Success indicator:

- The team understands the situation and prioritizes correctly.

#### 2. Containment of Impact

Stop the issue from spreading. Isolate failing components or disable faulty deployments.

Success indicator:

- The blast radius stops growing.

#### 3. Service Stabilization

Restore partial or degraded service if full restoration is not yet possible.

Examples:

- Failover
- Rollback
- Temporary scaling

Success indicator:

- Users regain access to core functionality.

#### 4. Clear Communication

Keep stakeholders updated with accurate and timely information.

Stakeholders may include:

- Internal teams
- Leadership
- Customers

Success indicator:

- No confusion about status, ownership, or next steps.

## 4. Recovery

### Definition

**Recovery** is the set of actions taken after an incident has been contained to restore systems, services, and operations to normal.

### Characteristics

- Timeframe: hours to days or weeks
- Primary goal: restoration and improvement
- Focus: root cause, integrity, resilience
- Audience: engineers, SREs, and leadership

### Recovery Goals

There are four main recovery goals:

#### 1. Full Service Restoration

Return all systems and features to their intended state, not just a temporary workaround.

Success indicator:

- No lingering degradation or hidden issues.

#### 2. Root Cause Identification

Find the underlying cause and implement fixes to prevent recurrence.

Success indicator:

- The same incident cannot easily happen again.

#### 3. Data Integrity and Validation

Make sure no data loss, corruption, or inconsistency remains.

Success indicator:

- Systems pass validation checks and audits.

#### 4. Resilience Improvement

Strengthen systems, processes, and monitoring based on lessons learned.

Examples:

- Improved alerting
- Better automation
- Stronger architecture

Success indicator:

- The system is more resilient than before.

## 5. Difference Between Response and Recovery

|Dimension|Response|Recovery|
|---|---|---|
|Purpose|Containment and stabilization|Restoration and normalization|
|Scope|Immediate symptoms and impact|Root causes and long-term fixes|
|Duration|Minutes to hours|Hours to days|
|Focus|Speed and impact reduction|Completeness and resilience|

Common misconception:  
Teams may think “we fixed it” once the system is back online. However, that usually means the **Response** phase is done, not the **Recovery** phase. Recovery still requires validation, cleanup, RCA, and long-term fixes.

## 6. Process: Incident Handling Flow

The process should look like this:

1. Alert triggers.
2. Team enters Response phase.
3. Team detects, assesses, contains, stabilizes, and communicates.
4. Once the system is stable, the team transitions to Recovery.
5. Team restores full service.
6. Team identifies root cause.
7. Team validates data and system integrity.
8. Team improves resilience to prevent recurrence.

## 7. Real-World Scenarios

### Scenario 1: Database Outage

Response activities:

- Alerts trigger on database unavailability.
- On-call engineer confirms outage caused by root node failure.
- Traffic redirected to read replica or failover instance.
- Non-critical services temporarily disabled.

Response outcome:

- Service restored in degraded mode within 20 minutes.

Recovery activities:

- Investigate why the primary database failed.
- Rebuild or repair the primary node.
- Validate data consistency between primary and replica.
- Reintroduce full traffic.
- Restore disabled services.
- Improve monitoring or autoscaling.

Recovery outcome:

- Fully redundant and validated database cluster restored within 24 hours.

### Scenario 2: Security Breach — Compromised Credentials

Response activities:

- Detect suspicious login activity.
- Revoke compromised credentials.
- Terminate sessions.
- Block malicious IP addresses.
- Enable heightened monitoring and alerting.

Response outcome:

- Threat contained within 15 minutes.

Recovery activities:

- Audit access logs to determine breach scope.
- Rotate affected credentials and secrets.
- Patch vulnerabilities.
- Notify affected users or stakeholders if required.
- Implement stronger authentication controls, such as MFA.

Recovery outcome:

- Security posture improved and compliance obligations met over several days.

### Scenario 3: Service Degradation — API Latency Spike

Response activities:

- Alerts indicate increased latency and error rates.
- Engineers identify recent deployment as likely cause.
- Roll back the deployment.
- Temporarily scale infrastructure.

Response outcome:

- Latency reduced to normal within 10 minutes.

Recovery activities:

- Analyze faulty deployment.
- Identify performance bottlenecks.
- Fix inefficient queries or code paths.
- Add performance testing to CI/CD.
- Tune autoscaling and alert thresholds.

Recovery outcome:

- Long-term performance improvements and stronger deployment safeguards.

## 8. Key Takeaways from M5-3

- Response and Recovery are not the same.
- Response stabilizes the incident.
- Recovery restores systems fully and prevents recurrence.
- Skipping recovery leads to recurring incidents and technical debt.
- The transition from response to recovery happens once the system is stable, not when it is fully restored.
- RCA is required in recovery.
- High-performing teams treat both phases as separate disciplines.

---

# Notes for M5-4: Escalation Delay Risk

Source: 

## 1. Main Topic

This PDF focuses on **Escalation Delay Risk**, which is the risk created when a known issue is not escalated early enough.

## 2. What Escalation Delay Risk Means

Escalation Delay Risk happens when a problem is recognized but not raised to the right people quickly enough.

The main danger is:  
**Waiting too long to escalate.**

## 3. Common Causes of Escalation Delay

Escalation may be delayed because of:

- Trying to solve the issue alone.
- Fear of overreacting.
- No clear escalation matrix.
- Poor communication.
- Waiting for business hours.

## 4. Why Escalation Delay Is Dangerous

Delayed escalation can lead to:

- SLA breaches
- Data corruption
- Security exposure
- Major incidents
- Customer dissatisfaction

A key idea from the PDF:  
**The failure is not always the system. Sometimes the failure is the delay.**

## 5. What Escalation Should Be Based On

Escalate based on:

- Impact potential
- Business risk
- Customer impact

Do not base escalation on:

- Ego
- Fear
- Assumptions

Overall:  
**Early escalation reduces total damage.**

## 6. Process: Daily Risk Awareness Framework

The PDF gives a daily framework using four questions:

1. **Is this immediate or potential?**  
    Decide whether the issue needs urgent action or planned investigation.
2. **What is the worst-case scenario?**  
    Think about the maximum possible damage if the issue continues.
3. **Do I need to escalate?**  
    Decide whether the right people need to be informed now.
4. **Am I solving the symptom or root cause?**  
    Avoid only applying a temporary fix if the real cause still exists.

## 7. Key Takeaways from M5-4

### Proactive Problem Solving

Teams should identify potential issues early and create mitigation strategies.

### Reduced Disruptions and Costs

Early risk identification helps prevent costly disruptions, budget overruns, and schedule delays.

### Informed Decision-Making

A structured approach helps teams evaluate the impact of decisions and choose better actions.

### Improved Resource Optimization

Teams can focus resources on the highest-priority risks.

### Increased Success Rates

Structured risk management improves the chance of meeting goals and reduces uncertainty.

### Increased Stakeholder Confidence

Proactive risk management builds trust and shows control over potential problems.

---

# Overall Combined Process from M5-1 to M5-4

1. **Recognize the alert or issue.**
2. **Identify the type of risk:** technical, operational, security, customer, or business risk.
3. **Apply the risk formula:** Risk = Likelihood × Impact.
4. **Classify the risk:** immediate or potential.
5. **Use the 3-step triage process:**
    - Is impact happening now?
    - Will impact happen within minutes?
    - Is this a trend or warning signal?
6. **Choose the correct response:**
    - Immediate risk → respond in 0–5 minutes, mitigate, stabilize, escalate.
    - Potential risk → acknowledge within SLA, investigate, ticket, schedule remediation.
7. **During incidents, separate Response from Recovery:**
    - Response = detect, assess, contain, stabilize, communicate.
    - Recovery = restore, identify root cause, validate, improve.
8. **Escalate early when impact, business risk, or customer impact is significant.**
9. **After stabilization, do not stop. Complete recovery and RCA.**
10. **Use lessons learned to improve monitoring, runbooks, automation, and resilience.**