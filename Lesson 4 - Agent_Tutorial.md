
# 🧠 Lesson 4 — Building a Local Agent with LangChain + Ollama (WSL)

---

## 📚 What You Learned

In this lesson, we covered how to:

- Install and run [Ollama](https://ollama.com) entirely inside WSL (Linux)
- Set up a local LangChain agent using modular Python
- Stream responses interactively via a simple UI
- Fix common model interaction bugs (like type coercion)
- Make your agent remember and use tools

---

## 🧰 Tech Stack

| Component      | Tool              |
|----------------|------------------|
| LLM Backend    | Ollama (Mistral, LLaMA3, etc.) |
| Agent Framework| LangChain        |
| UI             | Streamlit        |
| Runtime        | Python (inside WSL) |

---

## 🛠️ Setup (Inside WSL Only)

### ✅ 1. Install Python & Virtual Environment

```bash
sudo apt update && sudo apt install python3 python3-pip python3-venv -y
python3 -m venv .venv
source .venv/bin/activate
```

### ✅ 2. Install Ollama Natively in WSL

```bash
curl -fsSL https://ollama.com/install.sh | sh
ollama serve
```

In a separate terminal, test:

```bash
curl http://localhost:11434
```

### ✅ 3. Pull a Model

```bash
ollama pull mistral
```

---

## 💾 Project Structure

```
your_project/
│
├── app.py
└── core/
    ├── agent.py
    ├── llm.py
    ├── memory.py
    ├── tools.py
    └── callbacks.py
```

---

## 🔤 core/llm.py

```python
from langchain_community.chat_models import ChatOllama
from .callbacks import StreamHandler

def get_llm():
    return ChatOllama(
        model="mistral",
        base_url="http://localhost:11434",
        temperature=0.7,
        streaming=True,
        callbacks=[StreamHandler()],
        system="You are a concise software tutor. Always explain with code examples.",
    )
```

---

## 🧠 core/tools.py

```python
from langchain.tools import tool

@tool
def square(n: int) -> int:
    try:
        num = int(n)
        return num * num
    except Exception as e:
        return f"Invalid input: {n} ({e})"

tools = [square]
```

---

## 🧠 core/memory.py

```python
from langchain.memory import ConversationBufferMemory

def get_memory():
    return ConversationBufferMemory(memory_key="chat_history", return_messages=True)
```

---

## 🤖 core/agent.py

```python
from langchain.agents import initialize_agent, AgentType
from .llm import get_llm
from .tools import tools
from .memory import get_memory

def build_agent():
    return initialize_agent(
        tools=tools,
        llm=get_llm(),
        memory=get_memory(),
        agent=AgentType.CHAT_CONVERSATIONAL_REACT_DESCRIPTION,
        verbose=True,
    )
```

---

## 💬 core/callbacks.py

```python
import streamlit as st
from langchain.callbacks.base import BaseCallbackHandler
from langchain.schema import AgentAction, AgentFinish
from typing import Any

class StreamHandler(BaseCallbackHandler):
    def __init__(self):
        self.text = ""
        self.container = st.empty()

    def on_llm_new_token(self, token: str, **kwargs: Any) -> None:
        self.text += token
        self.container.markdown(self.text + "▌")

    def on_agent_action(self, action: AgentAction, **kwargs: Any) -> None:
        st.session_state.tool_log.append(f"🔧 Used Tool: {action.tool} → Input: {action.tool_input}")

    def on_agent_finish(self, finish: AgentFinish, **kwargs: Any) -> None:
        st.session_state.tool_log.append(f"✅ Final Answer: {finish.return_values['output']}")
```

---

## 📲 app.py

```python
import streamlit as st
from core.agent import build_agent
from core.memory import get_memory

st.set_page_config(page_title="LangChain Agent UI", layout="wide")
st.title("🧠 Local Agent with Ollama + Tools + Memory")

if "chat_history" not in st.session_state:
    st.session_state.chat_history = []
if "tool_log" not in st.session_state:
    st.session_state.tool_log = []

agent = build_agent()
memory = get_memory()

with st.container():
    user_input = st.text_input("💬 Type your message:", key="input")
    if st.button("Send") and user_input:
        st.session_state.chat_history.append(("🧑", user_input))
        with st.chat_message("🧑"):
            st.markdown(user_input)

        with st.chat_message("🤖"):
            response = agent.run(user_input)
            st.session_state.chat_history.append(("🤖", response))

col1, col2 = st.columns(2)

with col1:
    st.subheader("📚 Memory")
    for msg in memory.chat_memory.messages:
        st.markdown(f"**{msg.type.capitalize()}:** {msg.content}")

with col2:
    st.subheader("🔍 Tool Log")
    for log in st.session_state.tool_log[-10:]:
        st.markdown(log)
```

---

## ▶️ Run the App

```bash
# Run ollama in one tab
ollama serve

# In another tab
source .venv/bin/activate
streamlit run app.py
```

---

## ✅ Final Notes

- You're using **Ollama 100% inside WSL**, no Windows networking needed
- The app streams responses using LangChain + Streamlit
- Agent tools like `square()` are safely type-coerced to avoid bugs

---

You're now fully ready for Lesson 5: 🕸️ Multi-Agent Reasoning with LangGraph!
