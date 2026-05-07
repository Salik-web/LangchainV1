# 06 — Middleware

## 🎯 Purpose
Explore LangChain middleware — automatic conversation summarization and human-in-the-loop approval workflows.

## 🧠 Theory

### What is Middleware?
Middleware sits between the user and the agent, intercepting and modifying behavior. LangChain provides two key middleware:

| Middleware | Purpose |
|-----------|---------|
| `SummarizationMiddleware` | Compresses long conversation histories |
| `HumanInTheLoopMiddleware` | Pauses before sensitive tool calls for approval |

### Summarization Triggers
The summarization middleware supports three trigger types:

| Trigger | Example | Description |
|---------|---------|-------------|
| `messages` | `("messages", 10)` | Trigger when message count exceeds 10 |
| `tokens` | `("tokens", 550)` | Trigger when token count exceeds 550 |
| `fraction` | `("fraction", 0.005)` | Trigger when context uses 0.5% of max window |

### Human-in-the-Loop Decisions
When the middleware interrupts, the human can:
- **`approve`** — Execute the tool call as-is
- **`edit`** — Modify the tool arguments before execution
- **`reject`** — Cancel the tool call entirely

---

## 📝 Code Walkthrough

### Cell 1–2 — Summarization Middleware Setup
```python
from langchain.agents import create_agent
from langchain.agents.middleware import SummarizationMiddleware
from langgraph.checkpoint.memory import InMemorySaver

agent = create_agent(
    model="groq:qwen/qwen3-32b",
    checkpointer=InMemorySaver(),
    middleware=[
        SummarizationMiddleware(
            model="groq:qwen/qwen3-32b",
            trigger=("messages", 10),   # Summarize when > 10 messages
            keep=("messages", 4)        # Keep only last 4 messages
        )
    ]
)
```
- `InMemorySaver` persists state between invocations
- When messages exceed 10, older ones are summarized into a single message
- Only the 4 most recent messages are kept

### Cell 3–4 — Token & Fraction Triggers (commented examples)
```python
# Token-based
SummarizationMiddleware(trigger=("tokens", 550), keep=("tokens", 200))

# Fraction-based
SummarizationMiddleware(trigger=("fraction", 0.005), keep=("fraction", 0.002))
```

### Cell 5 — Human-in-the-Loop Setup
```python
from langchain.agents.middleware import HumanInTheLoopMiddleware

agent4 = create_agent(
    model="groq:qwen/qwen3-32b",
    tools=[read_email, send_email],
    checkpointer=InMemorySaver(),
    middleware=[HumanInTheLoopMiddleware(
        interrupt_on={
            "send_email": {"allowed_decisions": ["approve", "edit", "reject"]},
            "read_email": False   # ← no approval needed
        }
    )]
)
```
- `send_email` requires human approval (sensitive action)
- `read_email` runs freely without interruption

### Cell 6 — Invoking with Interrupt
```python
result = agent4.invoke(
    {"messages": [HumanMessage(content="Send an email to john@test.com...")]},
    config=config4
)
# result contains '__interrupt__' with pending action details
```
The agent pauses and returns the proposed tool call for review.

### Cell 7 — Resuming with Edit
```python
from langgraph.types import Command

result = agent4.invoke(
    Command(resume={
        "decisions": [{
            "type": "edit",
            "edited_action": {
                "name": "send_email",
                "args": {
                    "recipent": "correct@test.com",
                    "subject": "Corrected Subject",
                    "body": "This was edited before sending"
                }
            }
        }]
    }),
    config=config4
)
```
The human edits the recipient, subject, and body before the email is sent.

---

## 🔑 Key Takeaways
- `SummarizationMiddleware` keeps context windows manageable (3 trigger types)
- `HumanInTheLoopMiddleware` adds approval gates for sensitive actions
- Interrupted agents resume with `Command(resume={...})`
- `InMemorySaver` checkpointer is required for middleware state management
- Tool-level control: decide which tools need approval and which don't
