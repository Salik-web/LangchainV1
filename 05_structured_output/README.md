# 05 — Structured Output

## 🎯 Purpose
Force LLM responses into specific schemas using Pydantic models, TypedDict, and dataclasses.

## 🧠 Theory

### Why Structured Output?
By default, LLMs return free-text. But applications need **structured data** — JSON objects, typed fields, nested structures. LangChain's `with_structured_output()` forces the model to return data matching a specific schema using **function calling** under the hood.

### Three Schema Options

| Approach | Returns | Best For |
|----------|---------|----------|
| **Pydantic `BaseModel`** | Validated object with methods | Production apps, complex validation |
| **`TypedDict`** | Plain dictionary | Lightweight, quick prototyping |
| **`dataclass`** | Dataclass instance | Standard Python, simple schemas |

### How It Works
1. Define a schema (Pydantic/TypedDict/dataclass)
2. Call `model.with_structured_output(Schema)`
3. The model is forced to return data matching the schema
4. LangChain parses the response into the specified type

---

## 📝 Code Walkthrough

### Cell 1 — Define a Pydantic Schema
```python
from pydantic import BaseModel, Field

class movie(BaseModel):
    title: str = Field(description="The Title of the movie")
    year: int = Field(description="The year when movie was released")
    director: str = Field(description="The director of the movie")
    rating: float = Field(description="The rating of the movie out of 10")
```
Field descriptions help the LLM understand what data to extract.

### Cell 2 — Bind Structured Output
```python
model_with_structured_output = model.with_structured_output(movie)
```
Wraps the model with a parser that forces output into the schema.

### Cell 3 — Unstructured vs Structured Comparison
```python
# Unstructured → long essay about Inception
model.invoke("Provide details of the movie inception")

# Structured → movie(title='Inception', year=2010, director='Christopher Nolan', rating=8.8)
model_with_structured_output.invoke("Provide details of the movie inception")
```

### Cell 4 — Include Raw Response
```python
model.with_structured_output(movie, include_raw=True)
# Returns: {'raw': AIMessage(...), 'parsed': movie(...), 'parsing_error': None}
```
Get both the raw AI message and parsed structure — useful for debugging.

### Cell 5 — Nested Schemas
```python
class actor(BaseModel):
    name: str
    role: str

class movie(BaseModel):
    title: str
    year: int
    cast: list[actor]    # ← nested list of objects
    genres: list[str]
    budget: float | None
```
Compose complex structures with nested objects and optional fields.

### Cell 6 — TypedDict Alternative
```python
from typing_extensions import TypedDict, Annotated

class MovieDetails(TypedDict):
    title: Annotated[str, ..., "The title of the movie"]
    year: Annotated[int, ..., "The year movie was released"]
```
Returns plain dictionaries instead of Pydantic objects.

### Cell 7 — Agent with Structured Response
```python
agent = create_agent(model="groq:qwen/qwen3-32b", response_format=contactinfo)
result = agent.invoke({...})
result["structured_response"]
```
Agents support `response_format` for structured output.

### Cell 8 — Dataclass Alternative
```python
from dataclasses import dataclass

@dataclass
class contactinfo:
    name: str
    email: str
    phone: int
```
Standard Python dataclasses work as schemas too.

---

## 🔑 Key Takeaways
- `with_structured_output()` forces typed data from LLMs
- Three options: **Pydantic** (richest), **TypedDict** (lightweight), **dataclass** (standard)
- Nested schemas enable complex data extraction
- `include_raw=True` returns both raw and parsed output
- Agents support `response_format` parameter
