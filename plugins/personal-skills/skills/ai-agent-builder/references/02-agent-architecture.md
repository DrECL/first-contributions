## 2. Agent Architecture & Design Patterns

### 2.1 Core Agent Loop

Every agent follows a fundamental observe → think → act → observe loop. The differences between architectures lie in how deeply they plan before acting and how they handle tool results.

+---------------------------------------------+

|                 AGENT LOOP                  |

|                                             |

|   Input/Observation                         |

|         |                                   |

|         v                                   |

|   +-------------+                           |

|   |    Think    | \<---------------------+   |

|   |  (Reason)   |                       |   |

|   +------+------+                       |   |

|          |                              |   |

|          v                              |   |

|   +-------------+    No more tools      |   |

|   |  Select     |------------------>    |   |

|   |  Action     |                  |    |   |

|   +------+------+                  |    |   |

|          |                         |    |   |

|          v                         v    |   |

|   +-------------+           +----------+|   |

|   |  Execute    |           |  Final   ||   |

|   |  Tool/API   |           |  Answer  ||   |

|   +------+------+           +----------+|   |

|          |                              |   |

|          v                              |   |

|   +-------------+                       |   |

|   |  Observe    |-----------------------+   |

|   |  Result     |                           |

|   +-------------+                           |

+---------------------------------------------+

### 2.2 Architecture Patterns

#### Pattern A: ReAct (Reason + Act)

Best for: open-ended research, customer support, tool-use agents.

**How it works:** The agent interleaves reasoning traces (Thought:) with actions (Action:) and observations (Observation:) in a single conversation thread.

REACT_SYSTEM_PROMPT = """

You are a research agent. For every task:

1. THOUGHT: Reason about what you know and what you need

2. ACTION: Choose one tool to call

3. OBSERVATION: Read the tool result

4. Repeat until you have enough information

5. FINAL ANSWER: Synthesize and respond

Available tools: {tool_list}

Format strictly:

Thought: \<your reasoning>

Action: \<tool_name>

Action Input: \<tool_arguments as JSON>

Observation: \<tool result — filled by system>

... (repeat)

Final Answer: \<your complete response>

"""

def react_agent(query: str, tools: dict, llm, max_iterations: int = 10) -> str:

    messages = [

        {"role": "system", "content": REACT_SYSTEM_PROMPT.format(

            tool_list="\\n".join(f"- {k}: {v['description']}" for k, v in tools.items())

        )},

        {"role": "user", "content": query}

    ]

    

    for iteration in range(max_iterations):

        response = llm.complete(messages)

        

        if "Final Answer:" in response:

            return response.split("Final Answer:")[-1].strip()

        

        # Parse Action / Action Input

        action_line = [l for l in response.split("\\n") if l.startswith("Action:")]

        input_line  = [l for l in response.split("\\n") if l.startswith("Action Input:")]

        

        if not action_line:

            break

        

        tool_name = action_line[0].replace("Action:", "").strip()

        tool_input = json.loads(input_line[0].replace("Action Input:", "").strip())

        

        # Execute tool

        if tool_name in tools:

            observation = tools[tool_name]["fn"](**tool_input)

        else:

            observation = f"Error: Unknown tool '{tool_name}'"

        

        messages.append({"role": "assistant", "content": response})

        messages.append({"role": "user", "content": f"Observation: {observation}"})

    

    return "Agent reached max iterations without a final answer."

#### Pattern B: Plan-and-Execute

Best for: complex multi-step workflows, code generation, structured report creation.

**How it works:** A planner LLM generates a complete task list first; executor agents complete each step sequentially or in parallel.

PLANNER_PROMPT = """

Given this goal: {goal}

Create a numbered execution plan. Each step must be:

- Atomic: one clear action

- Verifiable: has a concrete success criterion

- Independent (where possible): can run without other steps completing first

Output format:

PLAN:

1. [Step description] | SUCCESS: [verification criterion] | DEPS: [step numbers or NONE]

2. ...

"""

EXECUTOR_PROMPT = """

Execute this step exactly:

{step}

Context from previous steps:

{context}

Available tools: {tools}

Return:

- RESULT: what you produced

- STATUS: SUCCESS or FAILED

- NOTES: any issues or observations

"""

class PlanExecuteAgent:

    def __init__(self, planner_llm, executor_llm, tools):

        self.planner = planner_llm

        self.executor = executor_llm

        self.tools = tools

    

    def run(self, goal: str) -> dict:

        # Phase 1: Plan

        plan_response = self.planner.complete(

            PLANNER_PROMPT.format(goal=goal)

        )

        steps = self._parse_plan(plan_response)

        

        # Phase 2: Execute

        results = {}

        for step in self._topological_sort(steps):

            context = {k: v["result"] for k, v in results.items() if v["status"] == "SUCCESS"}

            result = self.executor.complete(

                EXECUTOR_PROMPT.format(

                    step=step["description"],

                    context=json.dumps(context, indent=2),

                    tools=list(self.tools.keys())

                )

            )

            results[step["id"]] = self._parse_result(result)

        

        return results

#### Pattern C: Reflexion

Best for: code debugging, essay writing, tasks that benefit from self-critique.

**How it works:** After each attempt, the agent evaluates its own output, stores a reflection in memory, and retries.

REFLEXION_EVALUATOR_PROMPT = """

Task: {task}

Attempt: {attempt}

Evaluate this attempt:

1. What did it get RIGHT? (be specific)

2. What did it get WRONG or MISS? (be specific)

3. What should the NEXT attempt do differently?

Score (0-10): 

Reflection: 

"""

class ReflexionAgent:

    def __init__(self, llm, max_attempts: int = 3, pass_threshold: float = 8.0):

        self.llm = llm

        self.max_attempts = max_attempts

        self.threshold = pass_threshold

        self.memory = []  # Persisted reflections

    

    def run(self, task: str) -> str:

        for attempt_num in range(self.max_attempts):

            # Inject prior reflections into context

            reflection_context = "\\n".join(

                f"Attempt {i+1} reflection: {r}" for i, r in enumerate(self.memory)

            )

            

            attempt = self.llm.complete(

                f"Task: {task}\\n\\nPrior attempt learnings:\\n{reflection_context}\\n\\nYour attempt:"

            )

            

            # Evaluate

            eval_response = self.llm.complete(

                REFLEXION_EVALUATOR_PROMPT.format(task=task, attempt=attempt)

            )

            score = float(re.search(r"Score (0-10):s*([d.]+)", eval_response).group(1))

            reflection = re.search(r"Reflection:s*(.+)", eval_response, re.DOTALL).group(1).strip()

            

            self.memory.append(reflection)

            

            if score >= self.threshold:

                return attempt

        

        return attempt  # Return best attempt after max tries

#### Pattern D: Tool-Use Agent (Function Calling)

Best for: API integrations, data retrieval, modern LLM APIs that support native tool calling.

import anthropic

def build_tool_agent(tools: list[dict], system: str = "") -> callable:

    """

    tools: list of Anthropic-format tool definitions

    Returns a function that runs the agent for a given query.

    """

    client = anthropic.Anthropic()

    

    def run(query: str, tool_executors: dict[str, callable]) -> str:

        messages = [{"role": "user", "content": query}]

        

        while True:

            response = client.messages.create(

                model="claude-opus-4-5",

                max_tokens=4096,

                system=system,

                tools=tools,

                messages=messages

            )

            

            # No tool calls — final answer

            if response.stop_reason == "end_turn":

                return response.content[0].text

            

            # Process tool calls

            tool_results = []

            for block in response.content:

                if block.type == "tool_use":

                    executor = tool_executors.get(block.name)

                    if executor:

                        result = executor(**block.input)

                        tool_results.append({

                            "type": "tool_result",

                            "tool_use_id": block.id,

                            "content": str(result)

                        })

            

            messages.append({"role": "assistant", "content": response.content})

            messages.append({"role": "user", "content": tool_results})

    

    return run

### 2.3 Architecture Selection Guide

| Goal | Pattern | Reason |
| :---- | :---- | :---- |
| Open-ended research | ReAct | Flexible, self-correcting, handles unknown paths |
| Multi-step report generation | Plan-Execute | Predictable, auditable, checkpointable |
| Code writing / debugging | Reflexion | Self-critique loop improves quality over iterations |
| API integration / tool calling | Tool-Use | Native LLM feature, lower latency, less prompt engineering |
| Customer support bot | ReAct + Tool-Use | Hybrid: structured tools with flexible reasoning |
| Batch data processing | Plan-Execute with parallel dispatch | Speed via parallelism, structured output |
| Creative tasks (writing, design) | Reflexion | Quality improves with each self-critique cycle |

### 2.4 Multi-Agent System Topologies

TOPOLOGY 1: Hub-and-Spoke (Orchestrator + Specialists)

                    +----------------+

                    |  Orchestrator  |

                    |  (Coordinator) |

                    +-------+--------+

          +-----------------+-----------------+

          v                 v                 v

   +------------+   +------------+   +------------+

   |  Research  |   |   Coder    |   |  Writer    |

   |   Agent    |   |   Agent    |   |   Agent    |

   +------------+   +------------+   +------------+

Use for: complex tasks needing specialized expertise per sub-domain.

Orchestrator decomposes goal -> dispatches -> aggregates results.

TOPOLOGY 2: Pipeline (Assembly Line)

  Input -> [Extractor] -> [Transformer] -> [Validator] -> [Writer] -> Output

Use for: ETL, document processing, multi-stage generation tasks.

Each agent only sees the previous stage's output.

TOPOLOGY 3: Competitive / Debate

  Query -> Agent A --+

                    +---> Judge Agent ---> Final Answer

  Query -> Agent B --+

Use for: decisions requiring multiple perspectives, factual verification,

high-stakes outputs where consensus improves reliability.

TOPOLOGY 4: Peer Network (Gossip/Consensus)

  Agent 1 \<---> Agent 2

     ^            ^

     |            |

     v            v

  Agent 4 \<---> Agent 3

Use for: simulation, emergent behavior research, distributed problem solving.

High coordination overhead — avoid for production automation.

---