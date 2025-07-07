# 🧠 Lesson 4 — Building a Modern Local Agent with LangChain + Ollama

---

## 📚 **What You'll Learn**

In this lesson, we'll build a modern agent using:
- **Updated LangChain APIs** (v0.1+)
- **Ollama** for local LLM hosting
- **Proper error handling** and type safety
- **Tool calling** with the new LangChain patterns
- **Memory management** and state persistence

---

## 🧰 **Tech Stack**

| Component | Tool | Version |
|-----------|------|---------|
| LLM Backend | Ollama | Latest |
| Agent Framework | LangChain | 0.1+ |
| UI | Streamlit | Latest |
| Runtime | Python 3.9+ | In WSL/Linux |

---

## 🛠️ **Setup (WSL/Linux)**

### ✅ **1. Install Dependencies**

```bash
# Update system
sudo apt update && sudo apt install python3 python3-pip python3-venv curl -y

# Create virtual environment
python3 -m venv .venv
source .venv/bin/activate

# Install Python packages
pip install langchain langchain-community langchain-ollama streamlit
```

### ✅ **2. Install Ollama**

```bash
# Install Ollama
curl -fsSL https://ollama.com/install.sh | sh

# Start Ollama service
ollama serve &

# Pull a model (in another terminal)
ollama pull llama3.1:8b
# Or for faster inference: ollama pull llama3.1:8b-instruct-q4_K_M
```

### ✅ **3. Test Ollama**

```bash
curl http://localhost:11434/api/generate -d '{
  "model": "llama3.1:8b",
  "prompt": "Why is the sky blue?",
  "stream": false
}'
```

---

## 💾 **Modern Project Structure**

```
agent_project/
├── app.py                 # Streamlit UI
├── requirements.txt       # Dependencies
└── src/
    ├── __init__.py
    ├── llm.py            # LLM configuration
    ├── tools.py          # Agent tools
    ├── memory.py         # Memory management
    ├── agent.py          # Agent setup
    └── callbacks.py      # Streaming callbacks
```

---

## 🔧 **Core Implementation**

### **requirements.txt**
```txt
langchain>=0.1.0
langchain-community>=0.0.20
langchain-ollama>=0.1.0
streamlit>=1.30.0
numpy>=1.24.0
```

### **src/llm.py**
```python
from langchain_ollama import ChatOllama
from typing import Optional, List, Any

def get_llm(
    model: str = "llama3.1:8b",
    temperature: float = 0.7,
    base_url: str = "http://localhost:11434",
    streaming: bool = True,
    callbacks: Optional[List[Any]] = None
) -> ChatOllama:
    """Create configured Ollama LLM instance."""
    return ChatOllama(
        model=model,
        base_url=base_url,
        temperature=temperature,
        callbacks=callbacks or [],
        # Modern parameters
        num_predict=2048,  # Max tokens to generate
        top_p=0.9,
        top_k=40,
        repeat_penalty=1.1,
    )
```

### **src/tools.py**
```python
from langchain.tools import tool
from typing import Union
import math
import json

@tool
def calculator(expression: str) -> str:
    """
    Evaluate mathematical expressions safely.
    
    Args:
        expression: Mathematical expression like "2 + 3 * 4"
        
    Returns:
        Result of the calculation or error message
    """
    try:
        # Safe evaluation - only allow math operations
        allowed_names = {
            "abs": abs, "round": round, "min": min, "max": max,
            "pow": pow, "sqrt": math.sqrt, "sin": math.sin, "cos": math.cos,
            "tan": math.tan, "log": math.log, "pi": math.pi, "e": math.e
        }
        
        # Parse and evaluate
        result = eval(expression, {"__builtins__": {}}, allowed_names)
        return f"Result: {result}"
        
    except Exception as e:
        return f"Error: {str(e)}"

@tool  
def search_web(query: str) -> str:
    """
    Simulate web search (replace with actual search API).
    
    Args:
        query: Search query
        
    Returns:
        Simulated search results
    """
    # In production, use DuckDuckGo, Serper, or Tavily API
    return f"Search results for '{query}': [This is a simulated search result. In production, integrate with a real search API.]"

@tool
def get_weather(location: str) -> str:
    """
    Get weather information for a location.
    
    Args:
        location: City name or location
        
    Returns:
        Weather information
    """
    # In production, use OpenWeatherMap or similar API
    return f"Weather in {location}: 72°F, partly cloudy (simulated - integrate with real weather API)"

# Export all tools
tools = [calculator, search_web, get_weather]
```

### **src/memory.py**
```python
from langchain.memory import ConversationBufferWindowMemory
from langchain.schema import BaseMessage
from typing import List, Dict, Any

class AgentMemory:
    """Enhanced memory management for agents."""
    
    def __init__(self, window_size: int = 10):
        self.chat_memory = ConversationBufferWindowMemory(
            k=window_size,
            memory_key="chat_history",
            return_messages=True,
            output_key="output"
        )
        self.tool_history: List[Dict[str, Any]] = []
        
    def add_tool_use(self, tool_name: str, tool_input: Any, tool_output: Any):
        """Track tool usage for debugging and context."""
        self.tool_history.append({
            "tool": tool_name,
            "input": tool_input,
            "output": tool_output,
            "timestamp": __import__('datetime').datetime.now().isoformat()
        })
        
        # Keep only last 20 tool uses
        if len(self.tool_history) > 20:
            self.tool_history = self.tool_history[-20:]
    
    def get_recent_tools(self, limit: int = 5) -> List[Dict[str, Any]]:
        """Get recent tool usage."""
        return self.tool_history[-limit:]
    
    def clear(self):
        """Clear all memory."""
        self.chat_memory.clear()
        self.tool_history.clear()

def get_memory() -> AgentMemory:
    """Factory function for memory."""
    return AgentMemory()
```

### **src/agent.py**
```python
from langchain.agents import create_react_agent, AgentExecutor
from langchain import hub
from langchain.schema import BaseMessage
from .llm import get_llm
from .tools import tools
from .memory import AgentMemory
from typing import Optional, List, Any

def create_agent(
    memory: AgentMemory,
    callbacks: Optional[List[Any]] = None,
    verbose: bool = True
) -> AgentExecutor:
    """Create a modern ReAct agent with tools and memory."""
    
    # Get LLM with callbacks
    llm = get_llm(callbacks=callbacks)
    
    # Get ReAct prompt from hub (or define custom)
    try:
        prompt = hub.pull("hwchase17/react")
    except Exception:
        # Fallback prompt if hub is unavailable
        from langchain.prompts import PromptTemplate
        prompt = PromptTemplate.from_template("""
Answer the following questions as best you can. You have access to the following tools:

{tools}

Use the following format:

Question: the input question you must answer
Thought: you should always think about what to do
Action: the action to take, should be one of [{tool_names}]
Action Input: the input to the action
Observation: the result of the action
... (this Thought/Action/Action Input/Observation can repeat N times)
Thought: I now know the final answer
Final Answer: the final answer to the original input question

Begin!

Question: {input}
Thought: {agent_scratchpad}
""")
    
    # Create agent
    agent = create_react_agent(
        llm=llm,
        tools=tools,
        prompt=prompt
    )
    
    # Create agent executor
    agent_executor = AgentExecutor(
        agent=agent,
        tools=tools,
        memory=memory.chat_memory,
        verbose=verbose,
        max_iterations=10,
        max_execution_time=60,
        early_stopping_method="generate",
        handle_parsing_errors=True,
        return_intermediate_steps=True
    )
    
    return agent_executor
```

### **src/callbacks.py**
```python
import streamlit as st
from langchain.callbacks.base import BaseCallbackHandler
from langchain.schema import AgentAction, AgentFinish, LLMResult
from typing import Any, Dict, List, Optional

class StreamlitCallbackHandler(BaseCallbackHandler):
    """Callback handler for Streamlit streaming and logging."""
    
    def __init__(self):
        self.container = None
        self.text = ""
        self.current_tool = None
        
    def on_llm_start(self, serialized: Dict[str, Any], prompts: List[str], **kwargs: Any) -> None:
        """Run when LLM starts running."""
        if 'tool_log' in st.session_state:
            st.session_state.tool_log.append("🧠 **Agent thinking...**")
    
    def on_llm_new_token(self, token: str, **kwargs: Any) -> None:
        """Run on new LLM token. Only applicable when streaming=True."""
        if self.container is None:
            self.container = st.empty()
        
        self.text += token
        self.container.markdown(self.text + "▌")
    
    def on_llm_end(self, response: LLMResult, **kwargs: Any) -> None:
        """Run when LLM ends running."""
        if self.container:
            self.container.markdown(self.text)
            
    def on_tool_start(self, serialized: Dict[str, Any], input_str: str, **kwargs: Any) -> None:
        """Run when tool starts running."""
        tool_name = serialized.get("name", "Unknown Tool")
        self.current_tool = tool_name
        
        if 'tool_log' in st.session_state:
            st.session_state.tool_log.append(f"🔧 **Using {tool_name}**")
            st.session_state.tool_log.append(f"   Input: `{input_str}`")
    
    def on_tool_end(self, output: str, **kwargs: Any) -> None:
        """Run when tool ends running."""
        if 'tool_log' in st.session_state and self.current_tool:
            # Truncate long outputs
            display_output = output[:200] + "..." if len(output) > 200 else output
            st.session_state.tool_log.append(f"   Output: `{display_output}`")
            
    def on_tool_error(self, error: Exception, **kwargs: Any) -> None:
        """Run when tool errors."""
        if 'tool_log' in st.session_state:
            st.session_state.tool_log.append(f"❌ **Tool Error**: {str(error)}")
    
    def on_agent_finish(self, finish: AgentFinish, **kwargs: Any) -> None:
        """Run when agent finishes."""
        if 'tool_log' in st.session_state:
            st.session_state.tool_log.append("✅ **Agent completed task**")
```

### **app.py**
```python
import streamlit as st
from src.agent import create_agent
from src.memory import get_memory
from src.callbacks import StreamlitCallbackHandler

# Page configuration
st.set_page_config(
    page_title="Local AI Agent",
    page_icon="🤖",
    layout="wide"
)

st.title("🤖 Local AI Agent with Ollama")
st.markdown("*Powered by LangChain + Ollama + Modern Python*")

# Initialize session state
if "memory" not in st.session_state:
    st.session_state.memory = get_memory()

if "tool_log" not in st.session_state:
    st.session_state.tool_log = []

if "messages" not in st.session_state:
    st.session_state.messages = []

# Sidebar
with st.sidebar:
    st.header("🛠️ Agent Configuration")
    
    # Clear memory button
    if st.button("🗑️ Clear Memory"):
        st.session_state.memory.clear()
        st.session_state.tool_log.clear()
        st.session_state.messages.clear()
        st.rerun()