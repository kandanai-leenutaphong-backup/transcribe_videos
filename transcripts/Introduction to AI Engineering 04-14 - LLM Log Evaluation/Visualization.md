# LLM Logging and Evaluation

**Visual goal:** See how agent traces, evaluation datasets, graders, feedback, and iteration connect from one user query to a measurable improvement workflow.

[Read the detailed summary](./Summary.md)

## Big picture

```mermaid
flowchart LR
    U["User query"] --> A["Application"]
    A --> S["Agent service"]
    S --> C["Retrieve context"]
    S --> M["Call AI model"]
    S --> T["Call tools"]
    C --> R["Final response"]
    M --> R
    T --> R
    S --> L["LangSmith trace"]
    R --> E["Evaluation"]
    L --> D["Debug and inspect"]
    E --> D
    D --> I["Improve prompt, tools, or agent"]
```

> **Mental model:** Logging is the agent's flight recorder. Evaluation is the scorecard. The scorecard says where quality is weak, and the flight recorder helps explain what happened.

## Visual learning path

1. Follow one query through the application and Agent Service.
2. Inspect the trace for context, model calls, tools, outputs, and latency.
3. Choose an evaluation method that matches the question being asked.
4. Use a golden test set when trusted reference answers are needed.
5. Read both scores and grader comments.
6. Combine low scores with traces to locate the behavior to improve.
7. Re-run evaluation, using batch APIs when immediate results are unnecessary.

## 1. What a trace captures

The basic chain uses LangSmith's `traceable`. The demonstrated LangChain agent can also be traced automatically when `LANGSMITH_TRACING=true`.

```mermaid
sequenceDiagram
    participant User
    participant App
    participant Agent
    participant Context
    participant Model
    participant Tool
    participant Trace

    User->>App: Submit question
    App->>Agent: Send user query
    Agent->>Context: Retrieve knowledge
    Context-->>Agent: Return context
    Agent->>Model: Send prompt and context
    Model-->>Agent: Request or draft response
    Agent->>Tool: Call tool when needed
    Tool-->>Agent: Return tool result
    Agent-->>App: Return final answer
    Agent->>Trace: Record steps and latency
```

### Trace evidence map

| Trace element | What it tells the developer |
|---|---|
| User input | What the agent was asked |
| Retrieved context | Which knowledge influenced the answer |
| Model call | What prompt or processing step occurred |
| Tool name and input | Whether the agent selected and called the right tool |
| Tool result | Whether external data caused the outcome |
| Final response | What the user received |
| Step duration | Where execution time was spent |

## 2. Demonstrated weather agent trace

```mermaid
flowchart TD
    Q["Compare Bangkok and Tokyo weather"] --> AG["Weather agent"]
    AG --> WB["Call Bangkok weather tool"]
    AG --> WT["Call Tokyo weather tool"]
    WB --> TB["Bangkok temperature"]
    WT --> TT["Tokyo temperature"]
    TB --> CMP["Compare results"]
    TT --> CMP
    CMP --> ANS["Answer which is warmer or cooler"]
    AG -.-> LOG["Trace every step"]
    WB -.-> LOG
    WT -.-> LOG
    CMP -.-> LOG
```

If the final comparison is wrong, the trace lets the team distinguish among incorrect tool selection, incorrect tool inputs, incorrect returned temperatures, and incorrect final reasoning.

## 3. Choose the evaluation method

```mermaid
flowchart TD
    START["Need to evaluate an LLM response"] --> NEED{"What evidence is available?"}
    NEED -->|User reactions| HF["Human feedback"]
    NEED -->|Reference answer and lexical task| NLP["BLEU or GLEU-style score"]
    NEED -->|Semantic quality| JUDGE["LLM quality grader"]
    NEED -->|Risk review| SAFE["Safety grader"]
    NEED -->|Organization rule| CUSTOM["Custom evaluator"]
    NEED -->|Tool-using agent| AE["Agentic evaluator"]
    HF --> OUT["Collect signal"]
    NLP --> OUT
    JUDGE --> OUT
    SAFE --> OUT
    CUSTOM --> OUT
    AE --> OUT
    OUT --> REVIEW["Review score and comments"]
```

| Evaluation method | Best aligned question | Benefit | Caveat from the lesson |
|---|---|---|---|
| Thumbs, comments, manual grade | Did the user like the answer? | Direct human signal | Depends on users providing feedback |
| BLEU or GLEU-style score | Does wording overlap with a reference? | Fast and no LLM token cost | Meaning can match while wording or order differs |
| LLM quality grader | Is the answer semantically good? | Supports richer judgments | Uses model tokens |
| Safety grader | Is the input or output harmful? | Can flag conversations for review | Criteria and flagged cases still need inspection |
| Custom prompt evaluator | Did the output meet our own rule? | Fits business-specific needs | Evaluator prompt must define the rule well |
| Agentic evaluator | Did the agent use tools and pursue the goal correctly? | Evaluates behavior, not only prose | Requires checks across multiple steps |

## 4. Golden test set to grader

Humans define trusted examples before reference-based evaluation can be meaningful.

```mermaid
flowchart LR
    SME["Human subject-matter expert"] --> DS["Golden test set"]
    DS --> Q["Test question"]
    DS --> REF["Reference answer"]
    Q --> AG["Run agent"]
    AG --> RESP["Agent response"]
    Q --> GR["Metric or LLM grader"]
    REF --> GR
    RESP --> GR
    GR --> SCORE["Score"]
    GR --> COMMENT["Explanation comment"]
    SCORE --> REVIEW["Review weak areas"]
    COMMENT --> REVIEW
```

The regular exercise example supplies a question, the generated answer, and a reference answer. The displayed correctness score is approximately `0.9`, accompanied by an explanation.

## 5. What the graders measure

```mermaid
flowchart TB
    INPUT["Question, context, response, and optional reference"] --> C["Correctness"]
    INPUT --> R["Relevance"]
    INPUT --> G["Groundedness"]
    INPUT --> S["Safety"]
    INPUT --> F["Custom friendliness"]
    INPUT --> A["Agentic behavior"]
    C --> C1["Accurate against expected answer"]
    R --> R1["Addresses the question"]
    G --> G1["Supported by supplied context"]
    S --> S1["Flags violence, harmful language, or sexual content"]
    F --> F1["Custom score from 0 to 1"]
    A --> A1["Correct tools, intent, and goal completion"]
```

### Metric and evidence mapping

| Dimension | Main evidence |
|---|---|
| Correctness | Response plus trusted reference answer |
| Relevance | Question plus response |
| Groundedness | Context or document plus response |
| Safety | User input and model output |
| Friendliness | Organization-defined grading prompt plus response |
| Agentic behavior | Goal, tool calls, intermediate behavior, and final answer |

## 6. Evaluation and observability feedback loop

```mermaid
flowchart LR
    BUILD["Build agent"] --> RUN["Run conversations or test set"]
    RUN --> TRACE["Collect traces"]
    RUN --> EVAL["Calculate scores and grader comments"]
    TRACE --> FIND["Locate failed step"]
    EVAL --> FIND
    FIND --> CHANGE["Adjust prompt, tools, context, or logic"]
    CHANGE --> RERUN["Re-run evaluation"]
    RERUN --> TRACE
    RERUN --> EVAL
```

The transcript supports this connection directly: safety flags can lead to deeper debugging and stronger prompts, while agentic evaluation can reveal incorrect tool use or direction. Traces then expose the corresponding execution steps.

## 7. Cost-aware evaluation

```mermaid
flowchart TD
    JOB["LLM-based evaluation job"] --> URGENT{"Are results needed immediately?"}
    URGENT -->|Yes| REAL["Use real-time API"]
    URGENT -->|No| BATCH["Consider batch API"]
    REAL --> COST1["Higher real-time pricing"]
    BATCH --> COST2["Lower batch pricing shown in Azure OpenAI"]
    COST1 --> RESULT["Scores and comments"]
    COST2 --> RESULT
```

The instructor recommends considering batch APIs for evaluation because grading often does not require an immediate response. No exact price or submission command is given.

## Check your understanding

1. Why is a final answer log insufficient for debugging a tool-using agent?
2. What is the difference between `traceable` instrumentation and `LANGSMITH_TRACING=true` in the demonstrations?
3. Why can a BLEU or GLEU-style score be low even when two answers express similar meaning?
4. Which inputs are needed to evaluate correctness, relevance, and groundedness?
5. How do an evaluation score and a trace play different roles in improving an agent?
