# Introduction to AI Engineering 04.13: LLM Logging and Evaluation

**Format:** Thai-language lecture with English technical terms  
**Source video:** `0413-LLM-log-eval.mov`  
**Duration:** Approximately 18.9 minutes  
**Primary tools:** LangSmith, LangChain, OpenEvals, Google Gemini, Azure Log Analytics Workspace, Azure Application Insights

## Overview

This lesson connects two essential parts of operating an LLM agent:

1. **Logging and tracing** record what happened during a user interaction.
2. **Evaluation** measures whether the resulting response or agent behavior was good enough.

The instructor first traces a basic LLM chain and a tool-using weather agent with LangSmith. The lesson then compares human feedback, traditional NLP scores, LLM-as-a-judge evaluators, safety checks, custom evaluators, and agentic evaluation.

The practical journey is:

```text
Application -> Agent service -> Model, context, and tools -> Logged trace
                                                        -> Evaluation
                                                        -> Investigation and improvement
```

## 1. Why Agent Logging Matters

A web application sends a user query to an **Agent Service**. That service might contain:

- LangGraph code
- LangChain code
- Custom agent logic
- Calls to an AI model
- Retrieval of knowledge or context
- Calls to one or more tools

The final answer alone does not explain how the system produced it. Logging should therefore preserve the important events between input and output, including:

- The user conversation or query
- Retrieved context
- Model calls
- Tool calls
- Intermediate steps
- Final response
- Time used by each step

This visibility helps a developer inspect a failed or suspicious answer instead of treating the agent as a black box.

## 2. Exercise Setup

The walkthrough uses the course's `05/observability` folder.

The setup described in the lesson is:

1. Copy `.env.example` to create a `.env` file.
2. Add the Google Gemini API key.
3. Create a LangSmith account.
4. Open LangSmith's custom application area and generate a LangSmith API key.
5. Put the key in `.env`.
6. Install the packages from `requirements.txt`.
7. Create and activate a virtual environment.
8. Run the examples from the `observability` folder.

For automatic LangSmith tracing, the environment contains:

```text
LANGSMITH_TRACING=true
```

The transcript describes the setup actions but does not provide complete shell commands.

## 3. Tracing a Basic LLM Chain

The first example runs `trace_chain.py`, a basic LLM service.

The service receives:

- A body of knowledge or context
- A prompt
- A user question

It processes four questions and returns answers. The code imports LangSmith's tracing helper:

```python
from langsmith import traceable
```

The relevant operation is decorated or wrapped with `traceable`, allowing LangSmith to capture how each response was produced from beginning to end.

In the LangSmith tracing view, the instructor inspects:

- Each question
- Its answer
- The context supplied to the model
- The sequence of processing steps
- The duration of each step

The main lesson is that a trace is more useful than a single output log because it exposes the internal path through the LLM workflow.

## 4. Tracing a Tool-Using Agent

The second example runs `trace_agent.py`.

This agent adds weather tools and receives a question that asks it to:

- Compare the weather in Bangkok and Tokyo
- Determine which location has the higher or lower temperature

The agent calls two tools, retrieves the temperatures for Bangkok and Tokyo, and combines those results into its final response.

Unlike the first example, this file does not explicitly add `traceable`. Tracing still works because `LANGSMITH_TRACING=true` enables automatic tracing for the supported LangChain flow.

The resulting trace shows:

1. User input
2. Agent processing
3. Weather tool call for Bangkok
4. Weather tool call for Tokyo
5. Tool results
6. Final comparison
7. Time spent in each step

This demonstrates why tracing is especially important for agents. A wrong final answer might come from tool selection, tool input, returned data, or the final reasoning step.

## 5. Observability Beyond LangSmith

LangSmith is used because it is convenient for learning, but the concept is not limited to one product.

The instructor shows that Microsoft Azure can provide a similar experience through:

- **Log Analytics Workspace**
- **Application Insights**

An Azure implementation can also expose the question, answer, agent operations, and calls made during execution. Moving from LangSmith to a cloud observability stack may require code changes, but the investigation model remains similar.

## 6. Evaluation Without an LLM

The lesson divides evaluation into approaches that do not use an LLM and approaches that use an LLM as a grader.

### 6.1 Human feedback

Users can evaluate an answer with:

- Thumbs up
- Thumbs down
- Comments
- A manually assigned grade

This is familiar from applications such as ChatGPT. It is direct evidence of user satisfaction, but it depends on people choosing to submit feedback.

### 6.2 Traditional NLP scores

The file `01_nlp_score` demonstrates traditional scoring that compares a generated response with a reference answer by examining words and their order. The transcript names **BLEU** and a related score pronounced as "GLEU."

The demonstrated scores are relatively low because semantically similar sentences can use different words or word order.

Advantages:

- Fast
- Does not spend LLM tokens
- Can reduce compute cost

Caveats:

- Word overlap does not reliably capture meaning.
- Changed word order can reduce the score.
- The instructor says these metrics are difficult to use effectively in production for open-ended LLM answers.

They remain an option when speed and low compute cost matter and the task fits lexical comparison.

## 7. Evaluation with an LLM as a Judge

The next example, described as `02_gemini_quality`, uses an LLM to grade an answer.

LLM evaluation consumes tokens, but it can assess qualities that are difficult to capture with word overlap. Behind the evaluator is a grading prompt. The lesson shows the general pattern:

```text
You are an expert data labeler.
Your task is to grade the response using the selected metric.
```

The code uses **OpenEvals**, described as a source of reusable evaluation prompts and evaluators from the LangChain ecosystem.

The example evaluates an answer to:

```text
What are the benefits of regular exercise?
```

The evaluator receives:

- The question
- Context where applicable
- The model's answer
- A reference answer

The output includes a numeric score and a comment explaining the grade.

### Demonstrated quality dimensions

The lesson discusses dimensions such as:

- **Correctness:** Is the answer accurate compared with the expected answer?
- **Relevance:** Does the answer address the question?
- **Groundedness:** Is the answer supported by the supplied document or context?

One displayed correctness result is approximately `0.9`. The explanatory comment is important because it helps a developer understand why the evaluator assigned that score.

## 8. Golden Test Sets and Datasets

Reference-based evaluation requires humans to prepare a **golden test set**:

```text
Test question + expected or reference answer
```

Subject-matter experts must know what a correct answer should look like for the use case and label the dataset accordingly. The evaluator then compares the agent's response with that reference.

This creates a supported connection between datasets and evaluation:

1. Humans define representative questions.
2. Humans provide trusted answers.
3. The agent runs against the test set.
4. Metrics or graders score the outputs.
5. Comments and low scores identify areas for investigation.

The transcript does not prescribe a dataset size, split strategy, or pass threshold.

## 9. Safety Evaluation

The safety example also uses evaluators or prompts from OpenEvals.

Safety scoring examines user input and model output from several perspectives, including whether content contains:

- Violence
- Harmful language or behavior
- Sexual content
- Other unsafe material

Detected issues can be flagged so the team can review the relevant conversation. The findings may lead to deeper debugging or stronger prompts and controls around the model.

The lesson also briefly mentions fairness and other available dimensions, while encouraging learners to inspect the detailed definitions of each evaluator.

## 10. Custom Evaluators

An organization is not limited to prebuilt metrics. It can write a custom grading prompt for its own requirements.

The demonstrated custom evaluator scores **friendliness** from `0` to `1`.

Other organization-specific checks mentioned include:

- Whether a required term is mentioned in the answer
- Whether blocked text appears
- Custom prompt-based quality rules
- A block list

This makes evaluation useful for product requirements that generic correctness or safety graders do not capture.

## 11. Agentic Evaluation

Evaluating an agent requires more than judging its final prose. An agent has prompts, goals, and tools, so evaluation can inspect whether it:

- Selected the correct tool
- Called tools appropriately
- Followed the intended direction or intent
- Answered the user's goal
- Behaved like the provided good example rather than the bad example

The demonstration contrasts a good and a poor agent result, with example scores of `1` and `0`.

This extends evaluation from:

```text
Is the final answer good?
```

to:

```text
Did the agent take the right actions to reach the goal?
```

Tracing and agentic evaluation therefore complement each other. A score indicates that behavior was good or bad, while the trace helps locate the step that produced it.

## 12. Cost Optimization with Batch APIs

LLM-based evaluation uses model APIs and consumes tokens. The instructor opens Azure OpenAI pricing and compares:

- Real-time model pricing
- Batch API pricing

Batch API usage is presented as a lower-cost option for evaluations that do not need an immediate response. This can make repeated or large evaluation runs more economical while still returning useful results.

The transcript does not provide exact prices, model names, or a batch submission command.

## Evaluation Method Mapping

| Method | Needs reference answer | Uses LLM tokens | Main strength | Main caveat |
|---|---:|---:|---|---|
| Human feedback | No | No | Direct user judgment | Feedback may be sparse |
| BLEU or GLEU-style scoring | Yes | No | Fast and inexpensive | Sensitive to words and order |
| LLM quality grader | Often, for correctness | Yes | Can judge semantic quality | Adds token cost |
| Safety grader | No fixed answer required | Yes in the shown approach | Flags risky conversations | Requires review and clear criteria |
| Custom evaluator | Depends on the rule | Yes in the prompt-based example | Matches organization-specific needs | Prompt quality affects grading |
| Agentic evaluator | Depends on the test | Yes in the shown approach | Judges tool use and goal completion | Must evaluate more than final text |

## Practical Exercises

- Configure `.env` with Gemini and LangSmith credentials.
- Install `requirements.txt` and run `trace_chain.py`.
- Inspect the four chain traces, including context, answers, steps, and latency.
- Run `trace_agent.py` and follow both weather tool calls in LangSmith.
- Compare explicit `traceable` instrumentation with automatic tracing through `LANGSMITH_TRACING=true`.
- Run `01_nlp_score` and observe how wording and word order affect the score.
- Run the Gemini quality evaluator and read both the numeric scores and comments.
- Create a small golden test set with questions and trusted reference answers.
- Inspect correctness, relevance, groundedness, and safety evaluators.
- Build a custom friendliness evaluator with a `0` to `1` score.
- Add a required-term or block-list check.
- Compare good and bad agent behavior using tool choice and goal completion.
- Review real-time and batch model pricing before scheduling a larger evaluation run.

## Takeaways

- Log the conversation, context, model calls, tools, intermediate steps, final answer, and latency.
- Use tracing to understand how an agent reached its answer, not only what it returned.
- LangSmith supports both explicit tracing and automatic tracing for the demonstrated workflow.
- Azure Log Analytics Workspace and Application Insights can support a similar observability goal.
- Human feedback and traditional NLP metrics avoid LLM grading costs but have different limitations.
- LLM-as-a-judge evaluators can measure correctness, relevance, groundedness, safety, and custom qualities.
- Reliable reference-based evaluation depends on a human-labeled golden test set.
- Agentic evaluation should inspect tool use, intent, and goal completion as well as the final response.
- Use evaluation comments and traces together to find areas that need improvement.
- Consider batch APIs when evaluation does not need real-time results and cost matters.
