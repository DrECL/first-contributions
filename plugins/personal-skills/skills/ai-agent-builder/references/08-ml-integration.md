## 8. ML Integration for Agents

### 8.1 LLM Provider Abstraction Layer

from abc import ABC, abstractmethod

from dataclasses import dataclass

import time

import anthropic

import openai

from tenacity import retry, stop_after_attempt, wait_exponential

@dataclass

class LLMResponse:

    content: str

    model: str

    input_tokens: int

    output_tokens: int

    cost_usd: float

    latency_ms: float

# Cost per 1K tokens (update as pricing changes)

PRICING = {

    "claude-opus-4-5":     {"input": 0.015,    "output": 0.075},

    "claude-haiku-3":      {"input": 0.00025,  "output": 0.00125},

    "gpt-4o":              {"input": 0.005,    "output": 0.015},

    "gpt-4o-mini":         {"input": 0.00015,  "output": 0.0006},

}

class LLMProvider(ABC):

    @abstractmethod

    def complete(self, prompt: str, **kwargs) -> LLMResponse:

        pass

    

    def estimate_cost(self, prompt: str, output_tokens_estimate: int = 500) -> float:

        model = getattr(self, "model", "unknown")

        pricing = PRICING.get(model, {"input": 0.01, "output": 0.03})

        input_tokens = len(prompt.split()) * 1.3  # rough approximation

        return (input_tokens / 1000 * pricing["input"] +

                output_tokens_estimate / 1000 * pricing["output"])

class AnthropicProvider(LLMProvider):

    def __init__(self, model: str = "claude-opus-4-5"):

        self.client = anthropic.Anthropic()

        self.model  = model

    

    @retry(stop=stop_after_attempt(3), wait=wait_exponential(min=1, max=10))

    def complete(self, prompt: str, max_tokens: int = 2048, **kwargs) -> LLMResponse:

        start = time.time()

        response = self.client.messages.create(

            model=self.model,

            max_tokens=max_tokens,

            messages=[{"role": "user", "content": prompt}],

            **kwargs,

        )

        latency = (time.time() - start) * 1000

        pricing = PRICING.get(self.model, {"input": 0.015, "output": 0.075})

        cost = (response.usage.input_tokens  / 1000 * pricing["input"] +

                response.usage.output_tokens / 1000 * pricing["output"])

        return LLMResponse(

            content=response.content[0].text,

            model=self.model,

            input_tokens=response.usage.input_tokens,

            output_tokens=response.usage.output_tokens,

            cost_usd=cost,

            latency_ms=latency,

        )

class OpenAIProvider(LLMProvider):

    def __init__(self, model: str = "gpt-4o-mini"):

        self.client = openai.OpenAI()

        self.model  = model

    

    @retry(stop=stop_after_attempt(3), wait=wait_exponential(min=1, max=10))

    def complete(self, prompt: str, max_tokens: int = 2048, **kwargs) -> LLMResponse:

        start = time.time()

        response = self.client.chat.completions.create(

            model=self.model,

            max_tokens=max_tokens,

            messages=[{"role": "user", "content": prompt}],

            **kwargs,

        )

        latency = (time.time() - start) * 1000

        usage   = response.usage

        pricing = PRICING.get(self.model, {"input": 0.005, "output": 0.015})

        cost = (usage.prompt_tokens     / 1000 * pricing["input"] +

                usage.completion_tokens / 1000 * pricing["output"])

        return LLMResponse(

            content=response.choices[0].message.content,

            model=self.model,

            input_tokens=usage.prompt_tokens,

            output_tokens=usage.completion_tokens,

            cost_usd=cost,

            latency_ms=latency,

        )

class FallbackProvider(LLMProvider):

    """Try primary provider; fall back to secondary on failure."""

    

    def __init__(self, primary: LLMProvider, fallback: LLMProvider):

        self.primary  = primary

        self.fallback = fallback

    

    def complete(self, prompt: str, **kwargs) -> LLMResponse:

        try:

            return self.primary.complete(prompt, **kwargs)

        except Exception as e:

            print(f"Primary LLM failed ({e}), falling back...")

            return self.fallback.complete(prompt, **kwargs)

### 8.2 Model Deployment for Agent Serving

# Dockerfile for agent service

FROM python:3.11-slim

WORKDIR /app

# Install dependencies first (layer caching)

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

# Copy source

COPY src/ ./src/

COPY config/ ./config/

# Health check endpoint

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 

  CMD curl -f http://localhost:8080/health || exit 1

EXPOSE 8080

CMD ["uvicorn", "src.agent_server:app", "--host", "0.0.0.0", "--port", "8080", "--workers", "4"]

# src/agent_server.py — FastAPI agent serving wrapper

from fastapi import FastAPI, HTTPException, BackgroundTasks

from pydantic import BaseModel

import asyncio

import uuid

app = FastAPI(title="Agent Service", version="1.0.0")

class AgentRequest(BaseModel):

    query:        str

    session_id:   str | None = None

    max_steps:    int        = 10

    metadata:     dict       = {}

class AgentResponse(BaseModel):

    result:     str

    session_id: str

    steps_used: int

    cost_usd:   float

    latency_ms: float

@app.get("/health")

async def health():

    return {"status": "ok", "version": "1.0.0"}

@app.post("/agent/run", response_model=AgentResponse)

async def run_agent(request: AgentRequest):

    session_id = request.session_id or str(uuid.uuid4())

    # ... agent execution logic ...

    return AgentResponse(

        result="...",

        session_id=session_id,

        steps_used=0,

        cost_usd=0.0,

        latency_ms=0.0,

    )

@app.post("/agent/run-async")

async def run_agent_async(request: AgentRequest, background: BackgroundTasks):

    job_id = str(uuid.uuid4())

    background.add_task(_run_agent_task, job_id, request)

    return {"job_id": job_id, "status": "queued"}

@app.get("/agent/status/{job_id}")

async def get_status(job_id: str):

    # Fetch from job store (Redis, DB, etc.)

    return {"job_id": job_id, "status": "unknown"}

async def _run_agent_task(job_id: str, request: AgentRequest):

    # Run agent and persist result

    pass

### 8.3 Model Monitoring for Agents

# Drift detection for agent input distributions

from scipy.stats import ks_2samp

import numpy as np

def detect_input_drift(

    reference_inputs: list[str],

    current_inputs: list[str],

    threshold_p: float = 0.05,

) -> dict:

    """

    Detect distribution shift in agent input queries using

    token length distribution as a proxy metric.

    """

    ref_lengths = np.array([len(t.split()) for t in reference_inputs])

    cur_lengths = np.array([len(t.split()) for t in current_inputs])

    

    stat, p_value = ks_2samp(ref_lengths, cur_lengths)

    

    return {

        "drift_detected": p_value \< threshold_p,

        "ks_statistic":   float(stat),

        "p_value":        float(p_value),

        "ref_mean_tokens": float(ref_lengths.mean()),

        "cur_mean_tokens": float(cur_lengths.mean()),

        "recommendation": (

            "Retrain or re-evaluate agent prompts — input distribution shifted significantly."

            if p_value \< threshold_p else

            "No significant drift detected."

        ),

    }

# Alert thresholds for agent monitoring

AGENT_ALERT_THRESHOLDS = {

    "p95_latency_ms":     {"warning": 2000,  "critical": 5000},

    "error_rate_pct":     {"warning": 1.0,   "critical": 5.0},

    "cost_per_query_usd": {"warning": 0.05,  "critical": 0.20},

    "tool_failure_rate":  {"warning": 0.05,  "critical": 0.15},

    "token_overflow_rate":{"warning": 0.02,  "critical": 0.10},

}

### 8.4 Serving Strategy Selection

| Strategy | Latency | Throughput | Cost | Use Case |
| :---- | :---- | :---- | :---- | :---- |
| FastAPI + Uvicorn | Low | Medium | Low | REST agent APIs, single-model |
| Ray Serve | Medium | Very High | Medium | Multi-model pipelines, scaling |
| Triton Inference | Very Low | Very High | Medium | GPU batch inference |
| Serverless (Lambda/Cloud Run) | Cold-start medium | Auto-scale | Pay-per-use | Bursty agent tasks |
| Streaming (SSE/WebSocket) | Apparent Low | Medium | Low | Conversational agents |

---