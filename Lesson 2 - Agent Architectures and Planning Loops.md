
# 🧠 Lesson 2 Summary: Agent Architectures and Planning Loops

---

## 🎯 Lesson Goal

Understand how agents "think" using different architectures like:
- **ReAct**
- **AutoGPT**
- **BabyAGI**
- **LangGraph**

Also, learn how these architectures handle planning, tool use, and loops — and how they differ from one another.

---

## 🔁 What Is a Planning Loop?

An **agent planning loop** is a recurring cycle:

1. **Think** (reason about the task)
2. **Act** (execute an API call/tool/etc.)
3. **Observe** (receive feedback from the action)
4. **Reflect or Retry** (if needed)

This loop continues until the agent:
- Reaches the goal
- Hits a failure condition
- Exits due to timeout or limits

---

## 🧠 Agent Architectures

### 1. **ReAct** – Reason + Act

> Models reason step-by-step, then choose an action, see the result, and repeat.

🧩 Prompt Pattern:
```
Thought: I need to find the capital of Spain.
Action: search("capital of Spain")
Observation: It's Madrid.
Final Answer: Madrid
```

✅ Powers many LangChain agents  
✅ Simple, interpretable, and low-overhead

---

### 2. **AutoGPT**

> Fully autonomous agents that break down goals and execute recursively.

- Creates **subgoals**
- Stores **intermediate results**
- Loops through **planning → execution → feedback**
- Uses memory to persist state across tasks

⚠️ Risks:
- Loops endlessly if not guided well
- More prone to hallucinations without guardrails

---

### 3. **BabyAGI**

> A simpler, task-queue-driven agent.

- Maintains a **task list**
- Uses an LLM to:
  - Generate tasks
  - Prioritize tasks
  - Execute each in order
- Adds new tasks dynamically based on context

✅ More stable than AutoGPT  
✅ Easy to customize  
✅ Great for research workflows

---

### 4. **LangGraph**

> A framework to model agents as **graphs of states and transitions**.

- Each node is a step (e.g., plan, act, retry)
- Supports:
  - Branching
  - Retries
  - Parallel paths
  - Shared memory between steps

✅ Ideal for multi-agent systems  
✅ Powerful for agents with memory + recovery logic

---

## 🧠 Architecture Comparison Table

| Agent Type | Planning Style | Memory | Tool Use | Strengths | Weaknesses |
|------------|----------------|--------|----------|-----------|------------|
| ReAct      | Think → Act → Observe | Optional | ✅ Yes | Interpretable, modular | Requires thoughtful prompting |
| AutoGPT    | Recursive Planner | ✅ Yes | ✅ Yes | Fully autonomous | Can be inefficient or stuck |
| BabyAGI    | Task Queue | ✅ Yes | ✅ Yes | Modular, extensible | Needs clear goals |
| LangGraph  | State Graph | ✅ Yes | ✅ Yes | Retry logic, branching | More setup complexity |

---

## 🧪 Interactive Quiz

**Match the agent type to its description:**

| Concept        | Description |
|----------------|-------------|
| A. ReAct       | 3. Thinks, acts, observes in loop |
| B. AutoGPT     | 4. Fully autonomous recursive goal agent |
| C. BabyAGI     | 1. Uses a task queue with priorities |
| D. LangGraph   | 2. Uses state graph to model steps and memory |

✅ **Answers**: A → 3, B → 4, C → 1, D → 2

---

## 💬 Side Track Exploration:
### ❓ *Are agent types defined by the model (Claude, GPT, Gemini)?*

**Short Answer: No.**

- LLMs (e.g., Claude, GPT, Gemini) are the **brains** — they generate text, reason, and interpret prompts.
- **Agents are systems** that:
  - Wrap the LLM with memory
  - Manage tool execution
  - Structure goals and retries
- Any LLM can be used to implement **any agent type**, if it supports basic prompting and reasoning.

✅ Agent type = **logic and structure**, not the underlying model  
✅ Your code defines the behavior; the LLM executes language-based reasoning

---

## ✅ Key Takeaways

| Concept        | Insight |
|----------------|---------|
| Planning Loops | Agents don't respond once — they iterate through think-act cycles |
| ReAct          | Thoughtful reasoning and tool use in prompt loop |
| AutoGPT        | Fully autonomous but harder to control |
| BabyAGI        | Modular and queue-driven for structured task management |
| LangGraph      | Graph-based modeling of agent steps with state, branching, and retries |
| Model ≠ Agent  | You define the agent behavior — the LLM just follows instructions |

---

## 🚀 Coming Next:

**Lesson 3: Frameworks and Ecosystems**  
We’ll explore:
- LangChain's building blocks
- How to create agents using Chains, Tools, and Prompts
- How it connects with LangGraph for advanced orchestration
