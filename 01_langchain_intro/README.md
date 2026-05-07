# 01 — LangChain Introduction

## 🎯 Purpose
Get started with LangChain — verify the installation, create your first agent, and understand how agents work with tools.

## 🧠 Theory

### What is an Agent?
An **agent** in LangChain is an LLM-powered system that uses the model as a reasoning engine. It can:
- Decide which tools to call based on user input
- Execute tools and process their results
- Generate a final human-readable response

The `create_agent()` function builds a **LangGraph** state machine — a directed graph where nodes represent the model and tool execution steps.

### LangGraph Architecture
When you create an agent, LangChain generates a graph with:
- **`__start__`** → Entry point
- **`model`** → The LLM processes messages and decides next action
- **`tools`** → Executes any requested tool calls
- **`__end__`** → Final output

---

## 📝 Code Walkthrough

### Cell 1 — Import & Version Check
```python
import langchain
from langchain_google_genai import ChatGoogleGenerativeAI
langchain.__version__  # → '1.2.17'
```
Imports LangChain and the Google Gemini integration. Prints the installed version to confirm setup.

### Cell 2 — Load Environment Variables
```python
import os
from dotenv import load_dotenv
load_dotenv()
os.environ['GOOGLE_API_KEY'] = os.getenv('GOOGLE_API_KEY')
```
Loads API keys from a `.env` file and sets them as environment variables for authentication.

### Cell 3 — Create a Basic Agent
```python
from langchain.agents import create_agent
llm = ChatGoogleGenerativeAI(model="gemini-2.5-pro-preview-05-06")
agent = create_agent(model=llm, system_prompt="You are a helpful assistant")
```
Creates a basic agent with Gemini Pro. The agent graph has a simple `model → end` flow since no tools are attached.

### Cell 4 — Agent with a Custom Tool
```python
def getweather(city: str) -> str:
    """Get Weather for a city"""
    return f"The Weather of the {city} is Rainy"

agent = create_agent(model=llm, tools=[getweather], system_prompt="You are a helpful assistant")
```
Adds a `getweather` tool. Now the agent graph has a loop: `model → tools → model`, because the agent can decide to call the tool and then process its result.

### Cell 5–6 — Invoking the Agent
```python
response = agent.invoke({"messages": [{"role": "user", "content": "what is the weather in newyork"}]})
```
Sends a user query. The agent decides to call `getweather("newyork")` and returns the result. *(These cells show quota errors due to API rate limits.)*

---

## 🔑 Key Takeaways
- `create_agent()` builds a LangGraph state machine
- Agents autonomously decide when to use tools
- Google Gemini integrates via `ChatGoogleGenerativeAI`
- The agent graph changes shape based on whether tools are attached
