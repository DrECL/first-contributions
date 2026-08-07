## 11. Agent Deployment & Monitoring

### 11.1 Deployment Checklist

Before deploying any agent to production:

**Infrastructure**

* [ ] Health check endpoint responds: GET /health -> {"status": "ok"}  
* [ ] Graceful shutdown handles in-flight requests  
* [ ] Rate limiting configured (protect upstream APIs)  
* [ ] Request timeouts set (prevent runaway agents)  
* [ ] Retry logic with exponential backoff on transient failures  
* [ ] Circuit breaker prevents cascade failures

**Observability**

* [ ] Structured logging (JSON) with correlation IDs  
* [ ] Trace propagation across agent calls  
* [ ] Metrics exported: request count, latency p50/p95/p99, error rate, token usage, cost  
* [ ] Alerts configured for critical thresholds

**Security**

* [ ] API keys stored in environment variables, never in code  
* [ ] Input sanitization before passing to LLM (prompt injection defense)  
* [ ] Output filtering for PII / sensitive data  
* [ ] Rate limits per user/tenant  
* [ ] Audit log of all tool executions

**Cost Controls**

* [ ] Per-request token budget enforced  
* [ ] Daily/monthly spend limits with alerting  
* [ ] Model fallback chain configured (expensive -> cheap)

### 11.2 Structured Agent Logging

import json

import logging

import time

import uuid

from contextlib import contextmanager

from dataclasses import asdict, dataclass

@dataclass

class AgentTraceEvent:

    trace_id:    str

    span_id:     str

    event_type:  str   # "llm_call" | "tool_call" | "agent_start" | "agent_end"

    timestamp:   float

    agent_id:    str

    model:       str | None = None

    tool_name:   str | None = None

    input_tokens:  int = 0

    output_tokens: int = 0

    cost_usd:    float = 0.0

    latency_ms:  float = 0.0

    error:       str | None = None

    metadata:    dict | None = None

class AgentTracer:

    def __init__(self, agent_id: str):

        self.agent_id = agent_id

        self.logger   = logging.getLogger("agent.trace")

    

    def log(self, event: AgentTraceEvent):

        self.logger.info(json.dumps(asdict(event)))

    

    @contextmanager

    def trace_llm_call(self, trace_id: str, model: str):

        span_id = str(uuid.uuid4())[:8]

        start   = time.time()

        event   = AgentTraceEvent(

            trace_id=trace_id, span_id=span_id,

            event_type="llm_call", timestamp=start,

            agent_id=self.agent_id, model=model,

        )

        try:

            yield event

        except Exception as e:

            event.error = str(e)

            raise

        finally:

            event.latency_ms = (time.time() - start) * 1000

            self.log(event)

    

    @contextmanager

    def trace_tool_call(self, trace_id: str, tool_name: str):

        span_id = str(uuid.uuid4())[:8]

        start   = time.time()

        event   = AgentTraceEvent(

            trace_id=trace_id, span_id=span_id,

            event_type="tool_call", timestamp=start,

            agent_id=self.agent_id, tool_name=tool_name,

        )

        try:

            yield event

        except Exception as e:

            event.error = str(e)

            raise

        finally:

            event.latency_ms = (time.time() - start) * 1000

            self.log(event)

### 11.3 Agent Health Dashboard Schema

# Metrics to expose at /metrics (Prometheus-compatible)

AGENT_METRICS = """

# HELP agent_requests_total Total requests processed

# TYPE agent_requests_total counter

agent_requests_total{agent_id="{agent_id}",status="success"} {success_count}

agent_requests_total{agent_id="{agent_id}",status="error"} {error_count}

# HELP agent_latency_seconds Request latency

# TYPE agent_latency_seconds histogram

agent_latency_seconds_bucket{{agent_id="{agent_id}",le="0.5"}} {bucket_0_5}

agent_latency_seconds_bucket{{agent_id="{agent_id}",le="1.0"}} {bucket_1_0}

agent_latency_seconds_bucket{{agent_id="{agent_id}",le="5.0"}} {bucket_5_0}

agent_latency_seconds_bucket{{agent_id="{agent_id}",le="+Inf"}} {bucket_inf}

# HELP agent_tokens_total Total tokens consumed

# TYPE agent_tokens_total counter

agent_tokens_total{{agent_id="{agent_id}",type="input"}} {input_tokens}

agent_tokens_total{{agent_id="{agent_id}",type="output"}} {output_tokens}

# HELP agent_cost_usd_total Total cost in USD

# TYPE agent_cost_usd_total counter

agent_cost_usd_total{{agent_id="{agent_id}"}} {total_cost}

# HELP agent_tool_calls_total Tool calls by name

# TYPE agent_tool_calls_total counter

{tool_call_metrics}

"""

### 11.4 Scaling Strategies

| Scale Level | Approach | Infrastructure |
| :---- | :---- | :---- |
| Single user | Single process, local SQLite | Dev machine or single VM |
| Small team (\< 50 users) | Multi-worker Uvicorn, shared PostgreSQL | Single server, 4-8 CPU |
| Medium (50-500 users) | Horizontal pod autoscaling, Redis cache | Kubernetes, load balancer |
| Large (500+ users) | Async task queue (Celery/Arq), vector DB cluster | Multi-region, CDN |
| Enterprise | Dedicated LLM endpoints, tenant isolation, SOC2 | Managed cloud |

---