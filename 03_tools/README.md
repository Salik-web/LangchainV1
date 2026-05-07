# 03 — Tools & Function Calling

## 🎯 Purpose
Create custom tools, bind them to a model, and execute the full tool-calling workflow.

## 🧠 Theory

### What Are Tools?
Tools extend an LLM's capabilities by letting it call external functions. The LLM doesn't execute the function itself — it **requests** a function call, and your code executes it.

### Tool-Calling Workflow
```
User Query → Model (decides to call tool) → Tool Execution → Model (generates response)
```
1. User sends a message
2. Model analyzes and returns `tool_calls` (name + arguments)
3. Your code executes the tool and creates a `ToolMessage`
4. Model receives the tool result and generates the final answer

### `@tool` Decorator
The `@tool` decorator converts any Python function into a LangChain tool. The function's **docstring** becomes the tool description — the LLM uses this to decide when to call it.

---

## 📝 Code Walkthrough

### Cell 1–2 — Model Setup
```python
model = init_chat_model("groq:qwen/qwen3-32b")
```

### Cell 3 — Define & Bind a Tool
```python
from langchain.tools import tool

@tool
def getweather(city):
    """Get Weather of a location"""
    return f"The weather of {city} is sunny"

model_with_tool = model.bind_tools([getweather])
```
- `@tool` converts the function to a LangChain tool
- `bind_tools()` attaches the tool schema to the model so it knows the tool exists

### Cell 4 — Trigger a Tool Call
```python
response = model_with_tool.invoke("what is the weather in Karachi")
```
The model returns an `AIMessage` with empty `content` but populated `tool_calls` — requesting `getweather("Karachi")`.

### Cell 5 — Inspect Tool Calls
```python
for tool_call in response.tool_calls:
    print(f"Tool call = {tool_call['name']}")   # → getweather
    print(f"Args = {tool_call['args']}")         # → {'city': 'Karachi'}
```

### Cell 6 — Full Tool Execution Loop
```python
messages = [{"role": "user", "content": "What is the weather in karachi"}]
ai_msg = model_with_tool.invoke(messages)
messages.append(ai_msg)

for tool_call in ai_msg.tool_calls:
    tool_result = getweather.invoke(tool_call)
    messages.append(tool_result)

final_response = model_with_tool.invoke(messages)
print(final_response.text)  # → "The weather in Karachi is sunny."
```
The complete loop: ask → tool call → execute → respond.

---

## 🔑 Key Takeaways
- `@tool` decorator converts functions into LangChain tools
- The docstring is critical — the LLM uses it to decide when to call the tool
- `bind_tools()` attaches tool schemas to the model
- Tool calling is a multi-step loop: request → execute → respond
