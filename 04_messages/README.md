# 04 — Messages & Conversation History

## 🎯 Purpose
Master LangChain's message system — different message types, role-based prompting, metadata, and multi-turn conversation management.

## 🧠 Theory

### Message Types
LangChain uses typed messages to structure conversations:

| Type | Purpose | Example |
|------|---------|---------|
| `SystemMessage` | Sets assistant persona/behavior | "You are a poetry expert" |
| `HumanMessage` | User input | "Write me a poem" |
| `AIMessage` | Model response | Generated text or tool calls |
| `ToolMessage` | Tool execution result | Weather data, API response |

### How Conversations Work
Conversations are **ordered lists of messages**. The model sees the entire history and responds in context. This enables:
- Multi-turn dialogue
- Role-based behavior shaping
- Tool result integration

### Message Metadata
Messages can carry metadata:
- **`name`** — Identifies the sender (useful in multi-user scenarios)
- **`id`** — Unique identifier for tracking messages
- **`usage_metadata`** — Token counts for cost tracking

---

## 📝 Code Walkthrough

### Cell 1 — Simple Text Prompt
```python
model.invoke("Please tell me what is artificial intelligence")
```
The simplest invocation — just pass a string.

### Cell 2 — SystemMessage + HumanMessage
```python
from langchain.messages import SystemMessage, HumanMessage, AIMessage

messages = [
    SystemMessage("You are poetry expert"),
    HumanMessage("Write me a poem on artificial intelligence")
]
response = model.invoke(messages)
```
The `SystemMessage` sets the persona to "poetry expert", so the model generates a poem instead of a technical explanation.

### Cell 3 — Detailed System Prompt
```python
system_message = SystemMessage(
    """You are a senior Python developer with expertise in web frameworks.
    Always provide code examples and explain your reasoning.
    Be concise but thorough in your explanations."""
)
```
More detailed system prompts produce more focused and professional responses.

### Cell 4 — Message Metadata
```python
human_msg = HumanMessage(content="Hello", name="Ally", id="msg_!23")
```
Messages support `name` and `id` metadata for tracking and identification.

### Cell 5 — Multi-turn Conversation
```python
message = [
    SystemMessage("You are a helpful assistant"),
    HumanMessage("Can you help me?"),
    AIMessage("I would be happy to help you with that question!"),
    HumanMessage("Whats 2 + 2?")
]
response = model.invoke(message)
```
Simulates conversation history by passing an ordered list with alternating human/AI messages.

### Cell 6 — Token Usage Tracking
```python
response.usage_metadata
# {'input_tokens': 51, 'output_tokens': 318, 'total_tokens': 369}
```

### Cell 7 — ToolMessage in Conversations
```python
ai_message = AIMessage(content=[], tool_calls=[{
    "name": "get_weather", "args": {"location": "San francisco"}, "id": "call_123"
}])
tool_message = ToolMessage(content="sunny, 72F", tool_call_id="call_!23")

messages = [HumanMessage("Whats the weather in San Francisco?"), ai_message, tool_message]
response = model.invoke(messages)
```
Shows how `ToolMessage` fits into conversations — linked to `AIMessage` via `tool_call_id`.

---

## 🔑 Key Takeaways
- Four message types: `SystemMessage`, `HumanMessage`, `AIMessage`, `ToolMessage`
- System messages shape the assistant's behavior/persona
- Messages support `name` and `id` metadata
- Multi-turn conversations are ordered lists of messages
- `usage_metadata` provides token counts for cost tracking
