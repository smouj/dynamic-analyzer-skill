name: dynamic-analyzer
description: AI-powered analyzer for dynamic analysis tasks, including runtime code execution, log parsing, performance profiling, and anomaly detection
version: 1.2.0
author: SMOUJBOT
tags:
  - analysis
  - ai
  - automation
  - dynamic
  - profiling
  - debugging
  - monitoring
dependencies:
  - openclaw-core >= 2.1.0
  - ai-engine (built-in)
  - python >= 3.8 (for custom scripts)
  - jq (for JSON log processing)
  - perf (for Linux performance profiling)
environment_variables:
  - DYNAMIC_ANALYZER_LOG_LEVEL: DEBUG/INFO/WARN/ERROR (default: INFO)
  - DYNAMIC_ANALYZER_MODEL: grok-code-fast-1 (default)
  - DYNAMIC_ANALYZER_TIMEOUT: seconds (default: 300)
  - DYNAMIC_ANALYZER_OUTPUT_DIR: /tmp/dynamic-analyzer (default)
supported_platforms:
  - linux
  - macos
  - windows (via WSL)
---

# Dynamic Analyzer Skill

## Purpose

The Dynamic Analyzer skill provides AI-powered dynamic analysis capabilities for real-time software execution, performance monitoring, and anomaly detection. It enables automated analysis of running applications, log streams, and system behaviors to identify issues, optimize performance, and ensure reliability.

### Real Use Cases

1. **Runtime Code Profiling**: Analyze Python applications during execution to detect memory leaks, CPU bottlenecks, and inefficient algorithms in production-like environments.
2. **Log Stream Analysis**: Process live log files from web servers (e.g., Nginx, Apache) to identify error patterns, security threats, and performance degradation in real-time.
3. **API Endpoint Monitoring**: Dynamically test RESTful APIs against predefined scenarios to detect response delays, status code anomalies, and data integrity issues.
4. **Database Query Optimization**: Profile SQL queries in a running MySQL/PostgreSQL database to suggest indexes, query rewrites, and connection pool adjustments.
5. **Container Health Checks**: Analyze Docker containers during runtime for resource usage spikes, network latency, and service dependencies failures.
6. **CI/CD Pipeline Validation**: Integrate with GitHub Actions/Jenkins to dynamically analyze build artifacts and deployment scripts for security vulnerabilities and performance regressions.

## Scope

This skill handles dynamic analysis tasks that require runtime execution and real-time data collection. It does not perform static code analysis (use static-analyzer for that).

### Specific Commands

- `/dynamic-analyze profile --target <app> --duration 60s --output profile.json`: Profile application performance for 60 seconds and save results.
- `/dynamic-analyze logs --stream /var/log/app.log --pattern "ERROR|WARN" --ai-insights`: Analyze live log stream for patterns and generate AI insights.
- `/dynamic-analyze api-test --url https://api.example.com --scenarios load_test.json --concurrency 10`: Test API endpoints with concurrent load scenarios.
- `/dynamic-analyze db-query --dsn mysql://user:pass@host/db --query "SELECT * FROM users" --explain`: Profile and explain database query execution.
- `/dynamic-analyze container --name myapp --metrics cpu,memory,network --alert-threshold 80`: Monitor container metrics and alert on thresholds.
- `/dynamic-analyze pipeline --repo smouj/myrepo --branch main --checks security,performance`: Validate CI/CD pipeline with dynamic checks.

## Detailed Work Process

### Step 1: Environment Setup
- Ensure dependencies are installed: `openclaw doctor --check-dependencies`
- Set environment variables if needed (e.g., `export DYNAMIC_ANALYZER_LOG_LEVEL=DEBUG`)
- Verify target system accessibility (e.g., SSH to remote server if analyzing distributed apps)

### Step 2: Target Configuration
- Identify analysis target (application binary, log file path, API endpoint, DB connection string)
- Configure analysis parameters (duration, sampling rate, output format)
- For AI-enhanced analysis, specify model and insight depth (e.g., `--ai-model grok-code-fast-1 --insights deep`)

### Step 3: Execution and Data Collection
- Launch analysis command with appropriate flags
- Monitor progress in real-time (use `--verbose` for detailed output)
- Collect runtime data: CPU usage, memory allocation, network I/O, thread states

### Step 4: AI-Powered Analysis
- Feed collected data to AI engine for pattern recognition
- Generate insights: anomaly detection, root cause suggestions, optimization recommendations
- Cross-reference with historical data if available (via `--history-db /path/to/db`)

### Step 5: Report Generation
- Output results to specified format (JSON, CSV, Markdown)
- Include visualizations if `--chart` flag is used (requires matplotlib dependency)
- Send notifications via Slack/Email if `--notify` is set

### Step 6: Verification
- Run verification checks: `openclaw verify dynamic-analyzer --session-id <id>`
- Compare results against baselines (e.g., expected performance metrics)
- Log analysis session for auditing

## Golden Rules

1. **Never analyze production systems without read-only access**: Use `--read-only` flag to prevent modifications during profiling.
2. **Set timeouts to prevent runaway processes**: Always specify `--timeout` to avoid indefinite execution.
3. **Isolate analysis environment**: Run in sandboxed containers or VMs to prevent interference with target systems.
4. **Respect rate limits**: For API testing, use `--rate-limit 100req/min` to avoid overwhelming services.
5. **AI insights are suggestions, not absolutes**: Always verify AI-generated recommendations manually before implementation.
6. **Backup data before destructive analysis**: If `--destructive` is needed (rare), create snapshots first.
7. **Log all sessions**: Enable `--audit-log` to maintain traceability for compliance.

## Examples

### Example 1: Profiling a Flask App
**Input Prompt:**
```
/dynamic-analyze profile --target python app.py --duration 120s --output flask_profile.json --ai-insights --verbose
```

**Expected Output:**
```
[INFO] Starting dynamic profiling of python app.py
[DEBUG] Sampling CPU every 1s, memory every 5s
[PROGRESS] 30s elapsed: CPU avg 45%, Memory peak 512MB
[AI INSIGHT] Detected memory leak in route /api/users - potential unbounded list growth
[PROGRESS] 120s elapsed: Analysis complete
[REPORT] Results saved to flask_profile.json
Recommendations:
- Implement pagination for /api/users endpoint
- Add garbage collection hints in hot loops
```

### Example 2: Log Analysis for Error Patterns
**Input Prompt:**
```
/dynamic-analyze logs --stream /var/log/nginx/error.log --pattern "5[0-9][0-9]" --ai-insights --alert-email admin@example.com
```

**Expected Output:**
```
[INFO] Monitoring log stream /var/log/nginx/error.log
[ALERT] Pattern match: 502 Bad Gateway at 2026-03-13 14:30:15
[AI INSIGHT] Cluster of 502 errors indicates upstream service failure
[RECOMMENDATION] Check backend health at http://backend:8080/health
[EMAIL SENT] Alert dispatched to admin@example.com
```

### Example 3: API Load Testing
**Input Prompt:**
```
/dynamic-analyze api-test --url https://api.example.com/v1/products --scenarios scenarios.json --concurrency 50 --duration 300s --output api_report.json
```

**Expected Output:**
```
[INFO] Starting API load test with 50 concurrent users
[METRICS] Response time avg: 245ms, p95: 1200ms
[ERROR] 3% requests failed with 429 (Rate Limited)
[AI INSIGHT] Rate limiting triggered at 100 req/s - consider increasing limits or implementing backoff
[REPORT] Detailed results in api_report.json with latency charts
```

## Rollback Commands

### General Rollback
- Stop ongoing analysis: `/dynamic-analyze stop --session-id <id>`
- Clean up temporary files: `rm -rf $DYNAMIC_ANALYZER_OUTPUT_DIR/*`
- Revert system changes (if any): `git checkout -- .` (for config files modified during analysis)

### Specific Rollbacks
- For profiling: `pkill -f "dynamic-analyzer profile"` and remove profile artifacts: `rm -f profile_*.json`
- For log analysis: Terminate stream: `kill $(pgrep -f "tail -f /var/log/app.log")`
- For API testing: Cancel load: `/dynamic-analyze api-test --cancel <session-id>` and restore API configs: `cp backup/api_config.json api_config.json`
- For DB analysis: Disconnect: `mysqladmin -u user -p shutdown` (if local test DB) and restore schema: `mysql -u user -p db < backup_schema.sql`
- For container monitoring: Stop monitoring: `docker exec myapp pkill -f dynamic-monitor` and reset metrics: `docker stats --no-stream myapp`

### Verification Steps
- Confirm no background processes: `ps aux | grep dynamic-analyzer`
- Check disk usage: `du -sh $DYNAMIC_ANALYZER_OUTPUT_DIR`
- Validate system integrity: `openclaw verify system --post-analysis`
- Review logs for errors: `grep "ERROR\|WARN" /var/log/openclaw/dynamic-analyzer.log`

### Troubleshooting Common Issues
- **Timeout exceeded**: Increase `--timeout` value or reduce `--concurrency`.
- **Permission denied**: Run with `sudo` or adjust file permissions (e.g., `chmod 644 /var/log/app.log`).
- **AI insights not generating**: Check `ai-engine` status: `openclaw status ai-engine`; restart if needed: `openclaw restart ai-engine`.
- **High resource usage**: Lower sampling rates (e.g., `--sample-rate 0.1`) or use `--lightweight` mode.
- **Inconsistent results**: Ensure target is in stable state; avoid running during peak hours.
- **Network errors in API testing**: Use `--proxy http://proxy.company.com:8080` or check DNS resolution.