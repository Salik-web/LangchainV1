# 02 — Model Integration

## 🎯 Purpose
Learn how to initialize models from different providers and explore the three core invocation patterns: `invoke`, `stream`, and `batch`.

## 🧠 Theory

### Multi-Provider Support
LangChain abstracts away provider differences. You can use:
- `init_chat_model("groq:qwen/qwen3-32b")` — Universal factory
- `ChatGroq(model="qwen/qwen3-32b")` — Provider-specific class

The `provider:model` syntax automatically selects the right integration.

### Three Invocation Patterns
| Method | Behavior | Use Case |
|--------|----------|----------|
| `invoke()` | Sends prompt, waits for full response | Simple Q&A |
| `stream()` | Yields tokens as they're generated | Real-time UX |
| `batch()` | Sends multiple prompts in parallel | Bulk processing |

---

## 📝 Code Walkthrough

### Cell 1 — Environment Setup
```python
os.environ["GROQ_API_KEY"] = os.getenv("GROQ_API_KEY")
```

### Cell 2 — Universal Model Init
```python
from langchain.chat_models import init_chat_model
model = init_chat_model("groq:qwen/qwen3-32b")
```
`init_chat_model()` with `provider:model` syntax automatically creates the right class.

### Cell 3 — Direct Provider Class
```python
from langchain_groq import ChatGroq
model2 = ChatGroq(model="qwen/qwen3-32b")
```
Alternative approach using the provider-specific class directly.

### Cell 4 — Basic Invoke
```python
response = model.invoke("Hello how are you")
```
Sends a prompt and waits for the complete response. Returns an `AIMessage` object.

### Cell 5 — Streaming
```python
for chunk in model.stream("Write me a 200 words paragraph on artificial intelligence"):
    print(chunk.text, end="|", flush=True)
```
Streams tokens as they're generated. Each `chunk` is an `AIMessageChunk` with a small piece of text.

### Cell 6 — Batch Processing
```python
response3 = model.batch([
    "Why do parrots talk?",
    "How do airplanes fly",
    "What is quantum computing"
])
```
Sends 3 prompts in parallel and returns all responses as a list.

### Cell 7 — Batch with Concurrency Limit
```python
model.batch([...], config={'max_concurrency': 5})
```
Limits parallel requests to avoid rate limiting.

---

## 🔑 Key Takeaways
- `init_chat_model("provider:model")` is the recommended initialization method
- `invoke()` for single calls, `stream()` for real-time, `batch()` for parallel
- `batch()` supports `max_concurrency` to manage API rate limits
