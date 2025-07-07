# 🧠 Quiz Part 1: Foundations & Core Concepts (Questions 1-17)

---

## 📚 **Coverage**
- **Lesson 0**: Foundations of LLMs and Prompting
- **Lesson 1**: What Is Agentic AI
- **Lesson 2**: Agent Architectures and Planning Loops

**Scoring for Part 1:**
- 15-17 correct: 🏆 Expert understanding
- 13-14 correct: 🥇 Strong grasp
- 10-12 correct: 🥈 Good foundation
- 7-9 correct: 🥉 Basic understanding
- Below 7: 📚 Review needed

---

## 🔤 **Foundations of LLMs (Questions 1-6)**

### **1. What are tokens in the context of LLMs?**
A) Complete words only  
B) Basic units of text that may be partial words, punctuation, or spaces  
C) Individual characters  
D) Full sentences  

<details><summary>💡 Answer & Explanation</summary>
<strong>B) Basic units of text that may be partial words, punctuation, or spaces</strong><br><br>
Tokens are the fundamental units LLMs process. A word like "unbelievable" might be split into ["un", "believ", "able"]. Understanding tokenization is crucial for managing costs and context limits.
</details>

---

### **2. What temperature setting would be most appropriate for tool calling in agents?**
A) 1.5 for maximum creativity  
B) 0.7 for balanced responses  
C) 0.0 for completely deterministic behavior  
D) 1.0 for natural randomness  

<details><summary>💡 Answer & Explanation</summary>
<strong>C) 0.0 for completely deterministic behavior</strong><br><br>
Tool calling requires precision and consistency. Using temperature=0.0 ensures the agent reliably calls the correct tools with proper parameters, preventing random variations that could break functionality.
</details>

---

### **3. What does top-p (nucleus sampling) control?**
A) The number of tokens to generate  
B) Which tokens are considered based on cumulative probability  
C) The speed of token generation  
D) The cost of API calls  

<details><summary>💡 Answer & Explanation</summary>
<strong>B) Which tokens are considered based on cumulative probability</strong><br><br>
Top-p filtering keeps only the most probable tokens that together sum to the specified probability mass. For example, top-p=0.9 means only consider tokens that make up 90% of the probability distribution.
</details>

---

### **4. Which temperature range is best for creative writing tasks?**
A) 0.0-0.2 (highly deterministic)  
B) 0.3-0.5 (moderately controlled)  
C) 0.8-1.2 (high creativity)  
D) 1.5+ (extremely random)  

<details><summary>💡 Answer & Explanation</summary>
<strong>C) 0.8-1.2 (high creativity)</strong><br><br>
Creative writing benefits from higher randomness to generate diverse, imaginative content. However, going above 1.2 often produces incoherent text that's too chaotic to be useful.
</details>

---

### **5. What is the primary limitation of LLMs that agents address?**
A) They're too expensive to use  
B) They're stateless between API calls  
C) They're too slow to respond  
D) They can't generate coherent text  

<details><summary>💡 Answer & Explanation</summary>
<strong>B) They're stateless between API calls</strong><br><br>
LLMs have no memory between separate API calls. Agents solve this by adding memory systems, conversation history, and persistent state to create coherent, contextual interactions.
</details>

---

### **6. How do modern developers systematically tune temperature and top-p?**
A) Manually adjust for each individual prompt  
B) Use automated evaluation pipelines and A/B testing  
C) Always use the default values  
D) Copy settings from online tutorials  

<details><summary>💡 Answer & Explanation</summary>
<strong>B) Use automated evaluation pipelines and A/B testing</strong><br><br>
Professional development uses systematic approaches like automated testing, evaluation metrics, and data-driven optimization rather than manual guessing or copying settings.
</details>

---

## 🤖 **What Is Agentic AI (Questions 7-11)**

### **7. What makes an AI system "agentic"?**
A) Using a more powerful language model  
B) Having memory, tools, and goal-driven behavior  
C) Running on faster hardware  
D) Being trained on more recent data  

<details><summary>💡 Answer & Explanation</summary>
<strong>B) Having memory, tools, and goal-driven behavior</strong><br><br>
Agentic AI is defined by its capabilities, not its underlying model. The combination of persistent memory, external tool access, and autonomous goal pursuit distinguishes agents from simple LLM applications.
</details>

---

### **8. What is the key architectural difference between an LLM app and an agentic system?**
A) Agentic systems use different language models  
B) LLM apps are faster and more efficient  
C) Agentic systems can loop, plan, and maintain state  
D) LLM apps are more cost-effective  

<details><summary>💡 Answer & Explanation</summary>
<strong>C) Agentic systems can loop, plan, and maintain state</strong><br><br>
LLM apps typically follow a linear input→process→output pattern. Agents add feedback loops, planning capabilities, and state persistence to handle complex, multi-step tasks autonomously.
</details>

---

### **9. Which is NOT a core property of agentic systems?**
A) Persistent memory across interactions  
B) Ability to use external tools  
C) Large model parameter count  
D) Goal-driven autonomous behavior  

<details><summary>💡 Answer & Explanation</summary>
<strong>C) Large model parameter count</strong><br><br>
Agent capabilities come from architecture and design, not model size. A well-designed agent with a smaller model often outperforms a large model without proper agent architecture.
</details>

---

### **10. What enables agents to handle complex, long-term tasks?**
A) Larger context windows alone  
B) Memory, planning, and tool orchestration  
C) Faster processing speed  
D) Better training data quality  

<details><summary>💡 Answer & Explanation</summary>
<strong>B) Memory, planning, and tool orchestration</strong><br><br>
Long-term task handling requires the ability to remember past actions, plan future steps, and coordinate multiple tools - capabilities that come from agent architecture, not just model improvements.
</details>

---

### **11. Which analogy best describes the LLM-to-agent relationship?**
A) LLM = car, Agent = driver  
B) LLM = brain, Agent = body  
C) LLM = calculator, Agent = smart intern  
D) LLM = book, Agent = library  

<details><summary>💡 Answer & Explanation</summary>
<strong>C) LLM = calculator, Agent = smart intern</strong><br><br>
Just as an intern uses a calculator as one tool among many to complete complex tasks, an agent uses an LLM as its reasoning component while adding memory, planning, and tool coordination capabilities.
</details>

---

## 🏗️ **Agent Architectures (Questions 12-17)**

### **12. What is the core operational pattern of ReAct agents?**
A) Research → Action → Conclusion  
B) Reasoning → Acting → Observing  
C) Request → Analyze → Complete  
D) Read → Analyze → Create → Test  

<details><summary>💡 Answer & Explanation</summary>
<strong>B) Reasoning → Acting → Observing</strong><br><br>
ReAct (Reasoning + Acting) agents follow a cycle where they think about what to do, take an action (like using a tool), observe the result, and then reason about the next step based on that observation.
</details>

---

### **13. Which agent architecture uses a task queue management approach?**
A) ReAct  
B) AutoGPT  
C) BabyAGI  
D) Simple tool-calling agents  

<details><summary>💡 Answer & Explanation</summary>
<strong>C) BabyAGI</strong><br><br>
BabyAGI maintains a prioritized task queue, creating, prioritizing, and executing tasks systematically. This structure provides better organization than AutoGPT's more chaotic approach.
</details>

---

### **14. What is a significant risk of AutoGPT-style autonomous agents?**
A) They consume too much memory  
B) They can loop endlessly without clear stopping criteria  
C) They can't access external tools  
D) They're too expensive to run  

<details><summary>💡 Answer & Explanation</summary>
<strong>B) They can loop endlessly without clear stopping criteria</strong><br><br>
AutoGPT agents can get stuck in infinite loops, continuously creating new tasks without making real progress toward their goals. This can result in substantial costs and no useful output.
</details>

---

### **15. LangGraph agents are best characterized as:**
A) Linear chains of operations  
B) State machines with defined transitions  
C) Simple tool-calling interfaces  
D) Chat-based conversational systems  

<details><summary>💡 Answer & Explanation</summary>
<strong>B) State machines with defined transitions</strong><br><br>
LangGraph models agent workflows as directed graphs where each node represents a state or action, and edges define possible transitions. This provides structured, controllable agent behavior.
</details>

---

### **16. Which architecture provides the most interpretable, step-by-step reasoning?**
A) AutoGPT  
B) BabyAGI  
C) ReAct  
D) LangGraph  

<details><summary>💡 Answer & Explanation</summary>
<strong>C) ReAct</strong><br><br>
ReAct agents explicitly show their reasoning process in a "Thought → Action → Observation" format, making their decision-making transparent and easy to debug or understand.
</details>

---

### **17. What primarily determines an agent's type - the model or the architecture?**
A) The underlying language model (GPT, Claude, etc.)  
B) The architectural patterns and control logic  
C) Both model and architecture equally  
D) The cloud platform hosting the system  

<details><summary>💡 Answer & Explanation</summary>
<strong>B) The architectural patterns and control logic</strong><br><br>
Agent type is defined by how it operates (ReAct loops, task queues, state graphs, etc.), not which LLM it uses. The same model can power different agent types depending on the surrounding architecture.
</details>

---

## 📊 **Part 1 Scoring Guide**

**Check your answers and count correct responses:**

- **15-17 correct (88-100%)**: 🏆 **Expert Level** - You have excellent foundational understanding. Ready for advanced topics!

- **13-14 correct (76-87%)**: 🥇 **Advanced** - Strong grasp of core concepts. Minor review of specific areas recommended.

- **10-12 correct (59-75%)**: 🥈 **Intermediate** - Good foundation with some gaps. Focus on areas you missed.

- **7-9 correct (41-58%)**: 🥉 **Beginner** - Basic understanding present. Recommend reviewing Lessons 0-2 carefully.

- **Below 7 correct (<41%)**: 📚 **Review Needed** - Fundamental concepts need reinforcement. Re-read lessons and try again.

---

## 🎯 **Key Topics to Review Based on Common Mistakes**

**If you missed Questions 1-2**: Review tokenization and temperature settings  
**If you missed Questions 3-6**: Focus on sampling parameters and modern tuning practices  
**If you missed Questions 7-11**: Strengthen understanding of what makes systems "agentic"  
**If you missed Questions 12-17**: Study different agent architecture patterns  

---

## 🚀 **Ready for Part 2?**

Once you score 13+ on Part 1, proceed to **Quiz Part 2: Implementation & Tools** covering:
- RAG and Vector Memory
- LangChain and Modern Frameworks  
- Tool Creation and Integration
- Memory Management

**Note**: These foundational concepts are critical for everything that follows. A solid Part 1 score ensures success in more advanced topics!