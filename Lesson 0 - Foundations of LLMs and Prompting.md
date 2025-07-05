# 📘 Lesson 0 Summary: Foundations of LLMs and Prompting

---

## 🔤 **What Are Tokens?**

- 🧩 **Tokens** are the basic units of text used by LLMs — not always full words.
- 🪄 A word like `"unbelievable"` might be split into: `["un", "believ", "able"]`
- 📏 Token count affects:
  - 📦 **Prompt length limits**
  - 💸 **Cost (API-based models often bill by token)**
  - 🧠 **Memory strategies in agents**

---

## 🔥 **Temperature – Controlling Randomness**

| Temperature | Behavior                               | Use Case                         |
|-------------|----------------------------------------|----------------------------------|
| `0.0`       | 🔒 Fully deterministic                 | Tool use, code, data parsing     |
| `0.7`       | 🎯 Balanced, slightly creative         | Conversations, planning          |
| `1.0+`      | 🎨 High creativity, less predictability | Brainstorming, story writing     |

- **High temp** = more diverse outputs  
- **Low temp** = more reliable and repeatable

---

## 🎯 **Top-p – Controlling the Sampling Pool**

> Top-p (nucleus sampling) controls how many tokens are considered by **cumulative probability**, not by count.

- `top-p = 1.0` → Use **all** token options (default behavior)
- `top-p = 0.9` → Limit to tokens that **together** account for 90% of total likelihood
- `top-p = 0.3` → Only sample from the **most likely** tokens

🔍 **Think of it as a safety net** — it cuts off rare/outlier token options.

---

## 🧠 **LLMs Are Stateless – Agents Fix That**

LLMs forget everything between calls unless you include the full context in the prompt.

🤖 **Agents fix this by adding:**
- 🗃️ **Memory**: past inputs, reflections, scratchpads
- 🛠️ **Tools**: APIs, search, plugins
- 🧭 **Planning**: multi-step goals and strategies

---

## 🧪 **How Developers Actually Tune Temperature & Top-p**

✅ They **do not** manually tweak settings per prompt. Instead, they:

- Run **parameter sweeps** and grid search
- Use **automated evals** and scoring
- Monitor with tools like:
  - [LangSmith](https://smith.langchain.com)
  - [AgentOps](https://www.agentops.ai/)
  - [TruLens](https://www.trulens.org/)
  - Custom logging dashboards
- Dynamically adjust settings in agents depending on the task type

🧠 Example in an agent:
```python
if task == "call_api":
    temperature = 0.0
elif task == "write_tweet":
    temperature = 0.9
