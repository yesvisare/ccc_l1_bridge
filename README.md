# Bridge M2.3 → M2.4: Observability to Self-Healing

This repository contains readiness validation materials for transitioning from M2.3 (Production Monitoring & Observability) to M2.4 (Error Handling & Reliability).

## Contents

1. **Bridge_M2.3_to_M2.4_Readiness.ipynb** - Interactive Jupyter notebook with validation checks
2. **bridge_M2_3_to_M2_4.md** - Source bridge script content
3. **grafana_panels_spec.json** - Grafana dashboard panel specifications (generated)
4. **prometheus_alert_rules.yml** - Prometheus alert rules configuration (generated)

## Quick Start

### Run the Readiness Notebook

```bash
jupyter notebook Bridge_M2.3_to_M2.4_Readiness.ipynb
```

The notebook performs 4 validation checks to ensure M2.3 infrastructure is ready for M2.4.

---

## Validation Checks

### Check 1: Prometheus /metrics Endpoint

**Validates:** Prometheus is scraping your RAG pipeline metrics.

**Local Environment:**
```bash
# Check if Prometheus is running
curl http://localhost:9090/targets

# Check if your app metrics endpoint is accessible
curl http://localhost:8000/metrics

# Expected: Prometheus-format metrics (text/plain)
# Look for metrics like:
#   rag_query_duration_seconds_bucket
#   rag_errors_total
#   rag_cache_hits_total
```

**Success criteria:**
- `http://localhost:9090/targets` shows your app as "State: UP"
- Last scrape timestamp <30 seconds ago
- `/metrics` endpoint returns 10+ metric lines

**Troubleshooting:**
- **Connection refused:** Start Prometheus with `prometheus --config.file=prometheus.yml`
- **Target DOWN:** Check firewall rules on port 8000, verify app is running
- **No metrics:** Ensure your app has `/metrics` endpoint instrumented

---

### Check 2: Grafana Dashboard Panels

**Validates:** Grafana displays key performance metrics.

**Local Environment:**
```bash
# Check if Grafana is running
curl http://localhost:3000/api/health

# Import the generated dashboard JSON
# 1. Open http://localhost:3000
# 2. Login (default: admin/admin)
# 3. Go to Dashboards → Import
# 4. Upload grafana_panels_spec.json
# 5. Select Prometheus data source
```

**Success criteria:**
- All 4 panels show data:
  1. p95 Latency (graph with data points)
  2. Error Rate % (percentage graph)
  3. Cache Hit Rate % (percentage graph)
  4. Cost Accumulation (USD over time)
- Time range: last 30 minutes minimum
- Graphs are not empty (data points visible)

**Troubleshooting:**
- **No data in panels:** Check Prometheus data source configured correctly
- **Empty graphs:** Verify your app has generated metrics (send test queries)
- **Panel errors:** Validate PromQL queries in Prometheus UI first

---

### Check 3: Alert Rules Configuration

**Validates:** Alert rules are configured and can trigger notifications.

**Local Environment:**
```bash
# Add alert rules to Prometheus config
# Edit prometheus.yml:
rule_files:
  - "prometheus_alert_rules.yml"

# Reload Prometheus config
curl -X POST http://localhost:9090/-/reload

# Verify rules loaded
curl http://localhost:9090/api/v1/rules | jq

# Check active alerts
curl http://localhost:9090/api/v1/alerts | jq
```

**Test Alert Triggering:**
```bash
# Method 1: Use promtool to test rules
promtool test rules prometheus_alert_rules.yml

# Method 2: Simulate high error rate
# Send multiple failing requests to your app
# to trigger HighErrorRate alert

# Method 3: Check AlertManager
curl http://localhost:9093/api/v2/alerts
```

**Success criteria:**
- 4 alert rules loaded (HighP95Latency, HighErrorRate, LowRateLimitHeadroom, CircuitBreakerOpen)
- Test alert can be triggered manually
- Notification arrives in Slack/email within 2 minutes
- Alert message includes metric value and threshold

**Troubleshooting:**
- **Rules not loading:** Check YAML syntax with `yamllint prometheus_alert_rules.yml`
- **Alerts not firing:** Verify metric names match your instrumentation
- **No notifications:** Check AlertManager config and webhook URLs

---

### Check 4: Structured Logging with Correlation IDs

**Validates:** Logs are in JSON format with correlation IDs for request tracing.

**Local Environment:**
```bash
# Run a test query
curl -X POST http://localhost:8000/query \
  -H "Content-Type: application/json" \
  -d '{"query": "What is RAG?"}'

# Find logs for this request
# Assuming logs go to stdout or app.log

# Search for correlation_id
grep -i "correlation_id" app.log | head -5

# Extract one correlation_id and trace full request
CORRELATION_ID="abc12345"
grep "$CORRELATION_ID" app.log

# Expected output: Multiple log entries showing:
#   1. Query received
#   2. Embedding generated
#   3. Vector search completed
#   4. LLM call completed
#   5. Response returned
```

**Example valid log entry:**
```json
{
  "timestamp": "2025-11-06T12:34:56.789Z",
  "level": "INFO",
  "message": "Query received",
  "correlation_id": "abc12345",
  "query": "What is RAG?",
  "user_id": "user_123"
}
```

**Success criteria:**
- All log entries are valid JSON (parse with `jq`)
- Every log entry includes `correlation_id` or `request_id` field
- Can trace full request lifecycle using single correlation_id
- Grep by correlation_id returns 5+ log entries per request

**Troubleshooting:**
- **Plain text logs:** Implement JSON logging library (structlog for Python, winston for Node.js)
- **Missing correlation_id:** Add to logging context at request start using contextvars/AsyncLocalStorage
- **Different IDs per entry:** Ensure context propagation through async calls

---

## Staging Environment Validation

### Check 1: Prometheus (Staging)

```bash
# Replace localhost with staging URL
curl https://prometheus.staging.yourcompany.com/targets

# Verify your staging app is listed as UP
```

### Check 2: Grafana (Staging)

```bash
# Access staging Grafana
open https://grafana.staging.yourcompany.com

# Import dashboard JSON and verify data
```

### Check 3: Alert Rules (Staging)

```bash
# Check staging Prometheus rules
curl https://prometheus.staging.yourcompany.com/api/v1/rules

# Trigger test alert in staging (coordinate with team)
```

### Check 4: Logs (Staging)

```bash
# Search staging logs (assuming centralized logging)
# Example for AWS CloudWatch:
aws logs filter-log-events \
  --log-group-name /app/staging \
  --filter-pattern "correlation_id"

# Example for Datadog:
datadog-cli logs search "correlation_id" --service=rag-app --env=staging
```

---

## Time Investment

- **Validation notebook execution:** 5-10 minutes
- **Local validation (all 4 checks):** 30-45 minutes
  - Check 1 (Prometheus): 10 min
  - Check 2 (Grafana): 10 min
  - Check 3 (Alerts): 15 min
  - Check 4 (Logs): 10 min
- **Staging validation:** 20-30 minutes
- **Fixing issues (if found):** 1-2 hours per issue

---

## Prerequisites for M2.4

Before starting M2.4 (Error Handling & Reliability), ensure:

- [ ] All 4 validation checks pass
- [ ] Prometheus scraping metrics every 15-30 seconds
- [ ] Grafana dashboards show last 30 minutes of data
- [ ] At least 1 alert rule tested successfully
- [ ] Logs searchable by correlation_id

**Do NOT skip validation.** M2.4 builds directly on M2.3 infrastructure. Starting M2.4 without working observability is like adding error handling you can't monitor.

---

## Generated Files

The notebook creates these files when run:

1. **grafana_panels_spec.json** - Dashboard configuration with 4 panels
2. **prometheus_alert_rules.yml** - 4 alert rules (3 for M2.3, 1 preview for M2.4)

Both files are production-ready and can be imported directly.

---

## Support

- **Issues:** If validation checks fail, see Troubleshooting sections above
- **Questions:** Consult M2.3 video materials for setup instructions
- **Bugs:** Report issues with the validation notebook

---

## What's Next

After completing validation:

1. Review M2.3 accomplishments (Section 1 of notebook)
2. Take 15-minute break (M2.4 is dense!)
3. Proceed to **M2.4 video: Error Handling & Reliability**

M2.4 covers:
- Retry logic with exponential backoff
- Circuit breaker patterns
- Graceful degradation strategies
- Request queuing and rate limiting

**Expected outcome:** Error rate drops from 2% to <0.1%

---

## Module 2 Completion

After M2.4, you'll have:
- ✓ Cost optimization (M2.1, M2.2)
- ✓ Production observability (M2.3)
- ✓ Self-healing resilience (M2.4)

**Next:** Module 3 - Cloud Deployment
