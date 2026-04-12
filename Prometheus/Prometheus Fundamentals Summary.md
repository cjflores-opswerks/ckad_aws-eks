
### 1. The Prometheus Architecture

You must understand the role of every component in the Prometheus ecosystem and how they interact.

- **Prometheus Server:** The heart of the system. It does three main things:
    
    - **Retrieval:** Pulls (scrapes) metrics from configured targets.
        
    - **TSDB (Time Series Database):** Stores the scraped metrics locally on disk.
        
    - **HTTP Server:** Serves queries (PromQL) from dashboards like Grafana or its own Web UI.
        
- **Exporters:** Lightweight binaries installed on targets that translate the target's internal metrics into a format Prometheus can read.
    
    - _Important note:_ You don't always need exporters if the application natively exposes Prometheus metrics (e.g., Kubernetes components).
        
- **Pushgateway:** Used **only** for short-lived, batch jobs (like a cron job or backup script) that don't live long enough for Prometheus to scrape them. The job pushes metrics to the Pushgateway, and Prometheus scrapes the Pushgateway.
    
- **Alertmanager:** Handles alerts sent by the Prometheus server. It takes care of deduplicating, grouping, and routing alerts to receivers like Email, Slack, or PagerDuty.
    

### 2. The Pull Mechanism (Scraping)

Unlike traditional monitoring systems that wait for agents to _push_ data to a central server, Prometheus uses a **pull-based** model.

- Prometheus reaches out to target endpoints (usually over HTTP at the `/metrics` path) at a regular interval (the `scrape_interval`) to fetch data.
    
- **Why pull?** It makes it easier to run multiple independent Prometheus instances, helps quickly identify when a target is down (the scrape simply fails), and prevents the server from being overwhelmed by a flood of pushed data.
    

### 3. The Data Model

Prometheus stores data as **Time Series**, meaning every data point is associated with a timestamp. Every time series is uniquely identified by its **metric name** and a set of **labels** (key-value pairs).

**The Four Metric Types:**

You will absolutely need to know the difference between these for the exam:

1. **Counter:** A cumulative metric that only goes **UP** (or resets to zero). Use cases: total HTTP requests, total errors, total distance driven.
    
2. **Gauge:** A metric that can go **UP or DOWN**. Use cases: current CPU usage, current memory consumption, active concurrent users.
    
3. **Histogram:** Samples observations (like request durations or response sizes) and counts them in configurable "buckets." It also provides a sum of all observed values. Great for calculating percentiles (e.g., 95th percentile response time) on the server side.
    
4. **Summary:** Similar to a histogram, it samples observations, but it calculates configurable quantiles directly on the client side. It is less flexible than a histogram but uses less server-side processing power.
    

### 4. Configuration (`prometheus.yml`)

You need to be familiar with the main blocks of the configuration file:

- `global`: Defines default settings like `scrape_interval` (how often to pull) and `evaluation_interval` (how often to evaluate alerting rules).
    
- `scrape_configs`: Defines the jobs and targets Prometheus should pull data from.
    
- `rule_files`: Points to external files containing recording rules and alerting rules.
    
- `alerting`: Configures how Prometheus connects to the Alertmanager.
    

### 5. Service Discovery

In modern, dynamic environments like Kubernetes or AWS, IP addresses change constantly. Hardcoding IPs in the `scrape_configs` is impossible.

- Prometheus uses **Service Discovery (SD)** to dynamically find targets.
    
- Common SD mechanisms include `kubernetes_sd_configs`, `ec2_sd_configs`, `consul_sd_configs`, and `file_sd_configs` (reading a JSON/YAML file that another tool updates).
    

