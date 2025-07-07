
# 🧠 Lesson 5 — Multi-Agent Reasoning with LangGraph

---

## 📚 What You Learned

This lesson introduced LangGraph and how it enables advanced multi-agent systems through graph-based flows.

---

## 🧩 Why Multi-Agent Systems?

| Limitation of Single Agent | Benefit of Multi-Agent |
|----------------------------|------------------------|
| Tries to do everything     | Delegates specialized tasks |
| Poor context control       | Isolates concerns across nodes |
| Limited reasoning scope    | Enables collaboration and memory sharing |
| Hard to scale or extend    | Easy to plug in new agent nodes |

---

## 🌐 LangGraph Fundamentals

| Concept   | Description |
|-----------|-------------|
| **Node**  | A function (agent, tool, etc.) |
| **Edge**  | Transition between nodes |
| **State** | Shared dictionary tracking flow |
| **GraphRunner** | Executes the flow |
| **Conditional Edges** | Branches based on runtime logic |

```python
builder.add_edge("planner", "executor")
builder.add_conditional_edges("checker", condition_fn)
```

---

## 🏗️ Example: Planner + Executor Graph

### 🔸 planner_node
Breaks a user input into steps.

```python
def planner_node(state):
    prompt = f"Break this task into 2-3 steps: {state['input']}"
    ...
```

### 🔸 executor_node
Takes each step and processes it using the LLM.

```python
def executor_node(state):
    prompt = f"Execute this step: {state['current_step']}"
    ...
```

---

## 🔁 How LangGraph Determines Next Node

- **Static edge:** `add_edge(from, to)`
- **Dynamic routing:** `add_conditional_edges(from, router_fn)`
- **Looping:** Use a state flag or counter to return to the same node

---

## 🧠 Memory in LangGraph

1. **State-as-memory**: Store all context in the shared `state` dict  
2. **LangChain memory objects**: Use `ConversationBufferMemory` per node

```python
state = {
  "input": "...",
  "messages": [...],
  "steps": [...],
  "current_step": "..."
}
```

---

## 🕸️ Multi-Agent Branching

Define multiple agents (nodes), and route based on user intent:

```python
def router(state):
    if "code" in state["input"]:
        return "executor"
    elif "research" in state["input"]:
        return "researcher"
    ...
```

| Agent Node | Role |
|------------|------|
| `planner`  | Breaks input into subtasks |
| `executor` | Handles one step at a time |
| `researcher` | Fetches context |
| `critic`   | Reviews and corrects results |

---

## 🧠 Key Takeaways

- LangGraph is like a **flowchart for AI agents**
- Each node is a specialized agent or logic block
- Shared state is your memory
- Conditional edges give you **true reasoning paths**
- Easy to extend with memory, tools, critics, and loops

---

You're now ready for **Lesson 6: Advanced Memory, Feedback Loops & Toolchains** 🎯
