# 🦜🔗 LangChain — Comprehensive Learning Repository

A hands-on learning repository covering **LangChain v1** core concepts — from basic introductions to advanced middleware patterns. Each notebook is in its own folder with a dedicated README that explains every code cell in detail.

---

## 📚 Table of Contents

| # | Folder | Topic | Description |
|---|--------|-------|-------------|
| 01 | [01_langchain_intro](01_langchain_intro/) | LangChain Introduction | Getting started, creating agents, adding tools |
| 02 | [02_model_integration](02_model_integration/) | Model Integration | Multi-provider models, streaming, batching |
| 03 | [03_tools](03_tools/) | Tools & Function Calling | Custom tools, binding, full execution loop |
| 04 | [04_messages](04_messages/) | Messages & Conversations | Message types, roles, metadata, multi-turn chat |
| 05 | [05_structured_output](05_structured_output/) | Structured Output | Pydantic, TypedDict, dataclass schemas |
| 06 | [06_middleware](06_middleware/) | Middleware | Summarization & Human-in-the-Loop workflows |

---

## 🧠 Overview & Theory

### What is LangChain?

**LangChain** is an open-source framework for building applications powered by Large Language Models (LLMs). It provides composable building blocks — **models, prompts, tools, chains, agents, and middleware** — to orchestrate complex AI workflows.

### Key Concepts Covered

#### 1. 🚀 Agents (`create_agent`)
LangChain agents use LLMs as reasoning engines to determine which actions to take. The `create_agent()` API creates a **LangGraph**-based agent that can:
- Accept a system prompt
- Bind tools for function-calling
- Manage conversation state via a graph

#### 2. 🔌 Model Integration
LangChain supports **multi-provider model initialization** through `init_chat_model()`, allowing you to seamlessly switch between providers like OpenAI, Groq, Google Gemini, and more using a unified interface (e.g., `"groq:qwen/qwen3-32b"`). The framework also provides:
- **`invoke()`** — Single synchronous call
- **`stream()`** — Token-by-token streaming
- **`batch()`** — Parallel execution of multiple prompts

#### 3. 🛠️ Tools & Function Calling
Tools extend an LLM's capabilities by allowing it to call external functions. In LangChain:
- Use the `@tool` decorator to define tools
- **Bind tools** to a model with `model.bind_tools([...])`
- The LLM decides *when* and *how* to call tools based on user queries
- Tool results are fed back to the LLM for final response generation

#### 4. 💬 Messages & Conversation History
LangChain uses a typed message system to structure conversations:
- **`SystemMessage`** — Sets the assistant's behavior/persona
- **`HumanMessage`** — User input (supports `name` and `id` metadata)
- **`AIMessage`** — Model responses (can include `tool_calls`)
- **`ToolMessage`** — Results returned from tool execution

Messages are passed as ordered lists to simulate multi-turn conversations.

#### 5. 📐 Structured Output
Force LLMs to return data in a specific schema using:
- **Pydantic `BaseModel`** — Type-safe, validated objects with field descriptions
- **`TypedDict`** — Lightweight dictionary-based schemas with annotations
- **`dataclass`** — Python standard dataclasses
- **`with_structured_output()`** — Binds the schema to the model
- **Nested schemas** — Compose complex structures (e.g., `Movie` containing `Actor` list)

#### 6. 🔄 Middleware
Middleware intercepts and modifies agent behavior:
- **`SummarizationMiddleware`** — Automatically summarizes conversation history when it exceeds a threshold (messages, tokens, or fraction)
- **`HumanInTheLoopMiddleware`** — Pauses execution before sensitive tool calls, allowing humans to **approve**, **edit**, or **reject** the action

---

## 🛠️ Tech Stack

| Component | Technology |
|-----------|-----------|
| Framework | LangChain v1.2.17 |
| LLM Providers | Google Gemini, Groq (Qwen3-32B) |
| Graph Runtime | LangGraph |
| Language | Python 3.14 |
| Package Manager | uv / pip |

---

## ⚙️ Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/<your-username>/Updated-Langchain.git
   cd Updated-Langchain
   ```

2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Set up environment variables**
   Create a `.env` file in the root directory:
   ```env
   GOOGLE_API_KEY=your_google_api_key
   GROQ_API_KEY=your_groq_api_key
   ```

4. **Run the notebooks**
   Open any notebook folder and run the `.ipynb` file in VS Code or Jupyter.

---

## 📁 Project Structure

```
├── 01_langchain_intro/
│   ├── 01.langchain_intro.ipynb
│   └── README.md
├── 02_model_integration/
│   ├── 02.model_integration.ipynb
│   └── README.md
├── 03_tools/
│   ├── 03.tools.ipynb
│   └── README.md
├── 04_messages/
│   ├── 04.messages.ipynb
│   └── README.md
├── 05_structured_output/
│   ├── 05.structuredOutput.ipynb
│   └── README.md
├── 06_middleware/
│   ├── 06.MIddleware.ipynb
│   └── README.md
├── .gitignore
├── requirements.txt
└── README.md                  ← You are here
```

---

## 📄 License

This project is for educational purposes. Feel free to use and modify.

---

*Built with ❤️ while learning LangChain*
