# 📘 Lesson 0 Summary: Foundations of LLMs and Prompting

---

## 🔤 **What Are Tokens?**

- 🧩 **Tokens** are the basic units of text used by LLMs — not always full words.
- 🪄 A word like `"unbelievable"` might be split into: `["un", "believ", "able"]`
- 📏 Token count affects:
  - 📦 **Prompt length limits** (context window)
  - 💸 **Cost (API-based models often bill by token)**
  - 🧠 **Memory strategies in agents**
  - ⚡ **Response speed (more tokens = slower generation)**

**💡 Pro Tip**: Use tools like [OpenAI's Tokenizer](https://platform.openai.com/tokenizer) to count tokens in your prompts.

---

## 🔥 **Temperature – Controlling Randomness**

Temperature controls how **deterministic** vs **creative** the model's outputs are:

| Temperature | Behavior | Use Case | Example |
|-------------|----------|----------|---------|
| `0.0-0.2` | 🔒 Highly deterministic | Tool use, code, data parsing, factual QA | "What's 2+2?" → Always "4" |
| `0.3-0.7` | 🎯 Balanced creativity | Conversations, explanations, analysis | General chat, tutoring |
| `0.8-1.2` | 🎨 High creativity | Brainstorming, creative writing | Story generation, ideation |
| `1.3+` | 🌪️ Very unpredictable | Experimental, artistic use | Surreal content, abstract art |

**⚠️ Important**: Temperature interacts with other sampling parameters - it's not the only factor controlling randomness.

---

## 🎯 **Top-p (Nucleus Sampling) – Controlling the Token Pool**

Top-p limits which tokens the model can choose from based on **cumulative probability**:

- `top-p = 1.0` → Consider **all** possible tokens (default)
- `top-p = 0.9` → Only consider tokens that together make up 90% of the probability mass
- `top-p = 0.1` → Very restrictive - only the most likely tokens

```
Example: If "cat" has 40% probability, "dog" has 30%, "bird" has 20%, "fish" has 10%
- top-p = 0.7 → Can only choose "cat" or "dog" (40% + 30% = 70%)
- top-p = 0.9 → Can choose "cat", "dog", or "bird" (90% total)
```

**🔄 Temperature vs Top-p**: 
- Temperature affects the **shape** of probability distribution
- Top-p affects which tokens are **available** for selection

---

## 🎛️ **Other Key Sampling Parameters**

| Parameter | What It Does | Typical Range |
|-----------|-------------|---------------|
| **Top-k** | Limits to top K most likely tokens | 10-100 |
| **Repetition Penalty** | Reduces likelihood of repeated phrases | 1.0-1.3 |
| **Max Tokens** | Limits response length | Model-dependent |

---

## 🧠 **LLMs Are Stateless – Agents Fix That**

**Key Insight**: LLMs have no memory between API calls. Each request is independent.

```python
# This WON'T work as expected:
response1 = llm.call("My name is Alice")
response2 = llm.call("What's my name?")  # Model doesn't remember Alice!
```

🤖 **Agents solve this by adding:**
- 🗃️ **Memory**: Conversation history, vector databases, knowledge graphs
- 🛠️ **Tools**: APIs, search engines, calculators, databases
- 🧭 **Planning**: Multi-step reasoning, goal decomposition
- 🔄 **Persistence**: State management across interactions

---

## 🧪 **How Developers Actually Tune Parameters**

Modern developers **don't** manually adjust temperature per prompt. Instead:

### ✅ **Systematic Approaches:**
- **A/B testing** with different parameter combinations
- **Grid search** across parameter space
- **Automated evaluation** pipelines
- **Context-aware adjustment** (agents set params based on task type)

### 🛠️ **Tools for Parameter Optimization:**
- [LangSmith](https://smith.langchain.com) - LangChain's evaluation platform
- [Weights & Biases](https://wandb.ai) - Experiment tracking
- [Helicone](https://helicone.ai) - LLM observability
- [LangFuse](https://langfuse.com) - Open-source LLM engineering platform

### 🧠 **Dynamic Parameter Setting in Agents:**
```python
def get_llm_params(task_type):
    if task_type == "code_generation":
        return {"temperature": 0.1, "top_p": 0.95}
    elif task_type == "creative_writing":
        return {"temperature": 0.9, "top_p": 0.95}
    elif task_type == "tool_calling":
        return {"temperature": 0.0, "top_p": 1.0}
    else:
        return {"temperature": 0.7, "top_p": 0.95}
```

---

## 🎯 **Essential Prompting Techniques**

| Technique | Description | Example |
|-----------|-------------|---------|
| **Chain-of-Thought** | Ask model to think step-by-step | "Let's think step by step..." |
| **Few-Shot Learning** | Provide examples in prompt | Show 2-3 input→output examples |
| **Role Prompting** | Give the model a specific role | "You are an expert Python developer..." |
| **Constraint Setting** | Specify format, length, style | "Respond in exactly 3 bullet points" |

---

## 🚀 **Next Steps**

Now that you understand LLM fundamentals, we'll explore how to build **stateful, tool-using agents** that can:
- Remember past interactions
- Use external tools and APIs  
- Plan multi-step solutions
- Recover from errors

**Coming Next: Lesson 1 - What Is Agentic AI?**