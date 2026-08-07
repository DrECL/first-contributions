## 7. Prompt Engineering & Optimization

### 7.1 Core Prompt Patterns

| Pattern | When to Use | Token Cost | Quality Gain |
| :---- | :---- | :---- | :---- |
| Zero-shot | Simple, well-defined tasks | Lowest | Baseline |
| Few-shot (3-5 examples) | Complex tasks, consistent format needed | Medium | High |
| Chain-of-Thought (CoT) | Reasoning, math, multi-step logic | Medium | High |
| Role Prompting | Domain expertise, specific perspective | Low | Medium |
| Structured Output | Need parseable JSON/XML | Low | High (reliability) |
| Tree-of-Thought | Complex problem solving, backtracking | High | Very High |
| Meta-prompting | Generating/optimizing other prompts | High | Very High |
| Self-consistency | High-stakes decisions (majority vote) | Very High | High |

### 7.2 Chain-of-Thought Implementation

COT_TEMPLATES = {

    # Standard CoT

    "standard": """

{task}

Think step by step:

1. First, identify what information is given

2. Determine what needs to be found

3. Work through the reasoning systematically

4. State your conclusion

Reasoning:

""",

    # Few-shot CoT

    "few_shot": """

Solve problems by thinking step by step.

Example 1:

Problem: {example_problem_1}

Reasoning: {example_reasoning_1}

Answer: {example_answer_1}

Example 2:

Problem: {example_problem_2}

Reasoning: {example_reasoning_2}

Answer: {example_answer_2}

Now solve:

Problem: {problem}

Reasoning:

""",

    # Zero-shot CoT (Kojima et al.)

    "zero_shot": "{task}\\n\\nLet's think step by step.",

    # Plan-then-execute CoT

    "plan_execute": """

{task}

Step 1 - Make a plan: List the sub-problems you need to solve, in order.

Step 2 - Execute: Work through each sub-problem, showing your reasoning.

Step 3 - Verify: Check your answer against the original question.

Begin:

""",

}

### 7.3 Structured Output Design

from typing import Literal

from pydantic import BaseModel, Field

# --- Define Output Schema -----------------------------------------------------

class SentimentAnalysis(BaseModel):

    summary:    str     = Field(..., max_length=200, description="Brief content summary")

    sentiment:  Literal["positive", "negative", "neutral", "mixed"]

    confidence: float   = Field(..., ge=0.0, le=1.0, description="Confidence 0-1")

    key_points: list[str] = Field(..., max_items=5, description="Up to 5 key points")

# --- Build Prompt with Schema -------------------------------------------------

def build_structured_prompt(content: str, schema: type[BaseModel]) -> str:

    schema_json = schema.model_json_schema()

    

    return f"""Analyze the following content.

Respond ONLY with valid JSON matching this schema:

{json.dumps(schema_json, indent=2)}

IMPORTANT:

- Start your response with {{

- End your response with }}

- No markdown code fences, no explanation outside the JSON

Content to analyze:

{content}

JSON response:"""

# --- Parse and Validate Output ------------------------------------------------

def parse_structured_output(response: str, schema: type[BaseModel]) -> BaseModel:

    # Strip markdown fences if present

    import re

    cleaned = re.sub(r"```(?:json)?s*|s*```", "", response).strip()

    

    # Find outermost JSON object

    start = cleaned.find("{")

    end   = cleaned.rfind("}") + 1

    if start == -1 or end == 0:

        raise ValueError(f"No JSON object found in response:\\n{response}")

    

    data = json.loads(cleaned[start:end])

    return schema.model_validate(data)

### 7.4 Prompt Optimization Workflow

STEP 1: Baseline

  python scripts/prompt_optimizer.py current_prompt.txt --analyze --output baseline.json

  Capture: token count, clarity score, issues found

STEP 2: Identify Problems

  | Issue              | Apply This Pattern              |

  |--------------------|----------------------------------|

  | Ambiguous output   | Add explicit format specification |

  | Too verbose        | Extract to few-shot examples      |

  | Inconsistent results| Add role/persona framing         |

  | Missing edge cases | Add constraint boundaries         |

  | Poor reasoning     | Add chain-of-thought trigger      |

  | Wrong format       | Add schema + format enforcement   |

STEP 3: Apply Optimizations

  python scripts/prompt_optimizer.py current_prompt.txt --optimize --output optimized.txt

STEP 4: Compare

  python scripts/prompt_optimizer.py optimized.txt --analyze --compare baseline.json

STEP 5: A/B Test

  Run both prompts against held-out evaluation set.

  Accept optimization only if: quality up AND cost \<= 1.2x baseline.

### 7.5 Meta-Prompting (Prompt Generation)

META_PROMPT_GENERATOR = """

You are an expert prompt engineer. Generate an optimized prompt for the following use case.

## Use Case

Task: {task_description}

Model: {model}

Expected input format: {input_format}

Expected output format: {output_format}

Edge cases to handle: {edge_cases}

Constraints: {constraints}

## Generate a prompt that:

1. Uses role framing appropriate for the task

2. Provides clear, unambiguous instructions

3. Includes 2-3 few-shot examples if appropriate

4. Specifies exact output format

5. Handles the listed edge cases

6. Is token-efficient (no redundancy)

Return the complete prompt, ready to use.

"""

def generate_prompt(

    task_description: str,

    model: str = "claude-opus-4-5",

    output_format: str = "JSON",

    edge_cases: str = "empty input, ambiguous cases",

    constraints: str = "respond in English only",

    input_format: str = "plain text",

) -> str:

    """Use an LLM to generate an optimized prompt for a given task."""

    import anthropic

    client = anthropic.Anthropic()

    

    response = client.messages.create(

        model=model,

        max_tokens=2000,

        messages=[{

            "role": "user",

            "content": META_PROMPT_GENERATOR.format(

                task_description=task_description,

                model=model,

                input_format=input_format,

                output_format=output_format,

                edge_cases=edge_cases,

                constraints=constraints,

            )

        }]

    )

    return response.content[0].text

### 7.6 Few-Shot Example Design

EXAMPLE DESIGN CHECKLIST:

[ ] 3-5 examples (more = diminishing returns + token cost)

[ ] Covers: simple case, edge case, complex case, negative case

[ ] Consistent format across all examples

[ ] Output format matches expected production output exactly

[ ] Examples do NOT appear in test set (data contamination)

[ ] Ordered: simple -> complex (progressive difficulty)

EXAMPLE TEMPLATE:

Input: {diverse_input}

Output: {correctly_formatted_output}

[Repeat for each example with blank line between]

Now apply to:

Input: {actual_input}

Output:

---