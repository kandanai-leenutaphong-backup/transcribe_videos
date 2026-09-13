# Introduction to AI Engineering 04.10: Coding a Multi-Agent System

**Format:** Thai-language lecture with English technical terminology  
**Source:** `introduction_to_ai_engineering_0410_-_coding_multi-agent_systems_with_me (360p).mp4`

## Overview

This session turns the previous multi-agent concepts into a working example using **LangGraph** and **Google Gemini**. The instructor builds an HR assistant with a supervisor agent and two specialized agents:

- A **Compensation Agent** for salary and bonus information
- A **Leave Agent** for leave balances and leave submission

The walkthrough demonstrates single-domain questions, cross-domain questions that require multiple handoffs, mock tools, agent prompts, and LangGraph node connections.

## 1. Multi-Agent Recap

A multi-agent system expands a single-agent design into a main agent connected to several smaller agents. The main agent acts as a supervisor and routes tasks to domain specialists.

The instructor restates three benefits:

- **More usable context:** each agent has its own limited context and instructions, so responsibilities can be distributed rather than forcing one model to hold every tool and prompt.
- **Better task performance:** an agent with 10, 20, or eventually 100 tools becomes difficult to control. Smaller agents can specialize in fewer tools.
- **Independence:** agents can be added, removed, or changed with less impact on the rest of the system, similar to microservices in web development.

## 2. HR Assistant Scenario

The scenario represents a new employee asking an HR assistant about compensation and leave.

The architecture contains:

1. **Supervisor or Main HR Agent**
   - Receives the user request.
   - Chooses the relevant specialist.
   - Reviews the specialist's result.
   - Routes to another specialist if the request contains multiple topics.

2. **Compensation Agent**
   - Handles salary questions.
   - Handles bonus calculations or bonus information.

3. **Leave Agent**
   - Retrieves leave balances.
   - Submits leave requests.

The examples focus on querying operational systems rather than searching HR documents. The instructor mentions systems such as Workday or SAP as the kind of organizational source a real implementation might integrate with.

## 3. Agent Tools

The exercise uses four mock tools.

### Compensation Tools

- `get_salary`: returns mock salary information for roles such as engineer, manager, and director.
- `get_bonus`: accepts salary and rating information and returns a calculated or mock bonus.

### Leave Tools

- A leave-balance tool: returns vacation and sick-leave balances for mock employee IDs such as `employee001` and `employee002`.
- `submit_leave`: simulates submitting a leave request.

The instructor cautions that a real leave system would require authentication and should derive employee identity securely rather than trusting a simple employee ID provided in a prompt.

## 4. Demonstrated Interactions

The notebook is first run as a complete example, then the implementation is explained.

### Leave Balance

A question about the leave balance for `employee001` is routed by the supervisor to the Leave Agent. The response reports the employee's available vacation and sick-leave days.

### Leave Submission

A request to submit vacation leave is also routed to the Leave Agent, which selects the leave-submission tool.

### Compensation

A salary or bonus question is routed to the Compensation Agent, which uses the compensation tools and returns the result.

### Cross-Domain Request

A single prompt asking for both compensation and leave balance demonstrates multi-agent collaboration:

1. The supervisor routes to the Compensation Agent.
2. The Compensation Agent returns its result.
3. Control returns to the supervisor.
4. The supervisor identifies the remaining leave question.
5. The request is handed to the Leave Agent.
6. The final response combines the required information.

This example illustrates why a supervisor is more than a one-time classifier. It can continue coordinating until all parts of a request have been handled.

## 5. Implementation with LangGraph

The code imports Google Gemini and LangChain or LangGraph components. Each specialist is created as a ReAct-style agent with:

- A model
- Its domain-specific tools
- Instructions describing its responsibilities

The **Compensation Agent** receives salary and bonus tools. The **Leave Agent** receives leave balance and submission tools.

The supervisor receives handoff tools such as:

- Transfer to Compensation Agent
- Transfer to Leave Agent

Its prompt describes the supervisor as an HR manager and explains what each specialist can do. Clear tool descriptions and prompts remain important because the model uses them to decide where work should go.

## 6. Graph Structure

The LangGraph contains three main nodes:

- Supervisor
- Compensation Agent
- Leave Agent

The graph starts at the supervisor. Each specialist connects back to the supervisor so the main agent can inspect the result and determine whether the user request is complete.

Conceptually:

```text
START -> Supervisor
Supervisor -> Compensation Agent -> Supervisor
Supervisor -> Leave Agent -> Supervisor
Supervisor -> END
```

As in the earlier LangGraph session:

- Nodes represent agents or processing stages.
- Edges define the permitted handoffs.
- Prompts and tool descriptions help the supervisor select the correct route.

The instructor notes that the implementation is adapted from available LangGraph multi-agent examples and recommends reviewing the framework documentation for additional patterns.

## Practical Exercise

To reproduce the walkthrough:

1. Install the required LangGraph, LangChain, and Google Gemini dependencies.
2. Configure the Gemini model as in earlier course exercises.
3. Define two compensation tools and two leave tools using mock data.
4. Create the Compensation and Leave ReAct agents with narrowly scoped prompts.
5. Create supervisor transfer tools for both specialists.
6. Define the Main HR Agent prompt so it understands each specialist's role.
7. Add the three nodes and connect both specialists back to the supervisor.
8. Test a leave-only question, a compensation-only question, a leave submission, and a combined question.

## Takeaways / Action Items

- Keep specialist agents focused on a narrow domain and a small tool set.
- Give the supervisor clear descriptions of each agent's responsibility.
- Connect specialists back to the supervisor so it can coordinate multi-step or multi-domain requests.
- Test combined requests, not only simple questions, to verify that every requested task is completed.
- Treat the employee and HR data in the notebook as mock data only.
- Add authentication and secure identity handling before connecting a leave or compensation agent to real systems.
- Review LangGraph multi-agent documentation and extend the exercise with additional HR specialists or tools.
