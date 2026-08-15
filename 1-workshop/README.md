# Prompt Engineering Workshop

A hands-on workshop covering prompt engineering techniques — from basic zero-shot prompting to system prompts, temperature control, and structured outputs.

## Prerequisites

- Python 3.12+
- [`uv`](https://docs.astral.sh/uv/) package manager

## Setup

### 1. Install `uv`

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Or via Homebrew on macOS:

```bash
brew install uv
```

### 2. Install dependencies

```bash
uv sync
```

This installs all required packages (`openai`, `python-dotenv`, `ipykernel`) into an isolated virtual environment.

### 3. Configure environment variables

Create a `.env` file in this directory:

**Option A: OpenAI API**

```env
OPENAI_API_KEY=sk-...
```

**Option B: Local LLM server (e.g. [jan.ai](https://jan.ai))**

```env
LOCAL_API_KEY=nusiss
LOCAL_API_URL=http://127.0.0.1:1337/v1
```

### 4. Open the notebook

```bash
uv run --with jupyter jupyter lab
```

This starts JupyterLab with access to the project's virtual environment. Open `workshop.ipynb` from the file browser, then select the appropriate client option in the setup cell (OpenAI or local server).

## Workshop Contents

| Part | Topics |
|------|--------|
| Part 1 | Zero-shot, few-shot, role-based, and clear instruction prompting |
| Part 2 | Chain-of-thought, step-by-step reasoning, output formatting, system vs user prompts, temperature control |

## Assignment

See [`assignment.md`](assignment.md) — build a natural language cafe order processing system that outputs structured JSON.

---

## DevKapil Assignment: Cafe Order Processing System

### Objective

This assignment uses prompt engineering to convert natural language cafe orders into structured JSON for a cafe ordering system.

The system accepts customer orders written in normal language and extracts ordered items, quantities, sizes, and options.

### System Prompt

```text
You are a cafe order parsing assistant.

Your task is to convert natural language cafe orders into strict JSON only.

Rules:
1. Return only valid JSON. Do not include explanation, markdown, or extra text.
2. The JSON must contain exactly these fields:
   - items: a list of ordered items
   - total_items: total quantity of all items
3. Each item must contain:
   - name: standardized item name in Title Case
   - quantity: integer quantity
   - size: small, regular, medium, or large
4. If size is not mentioned, use "regular".
5. Convert number words into integers, for example "two" = 2 and "three" = 3.
6. Merge duplicate items only if name, size, and options are the same.
7. If the customer mentions a modifier such as iced, decaf, extra hot, no sugar, or oat milk, include it in an "options" list for that item.
8. For combo or meal orders, split the meal into separate items if the items are clearly mentioned.
9. If quantity is not mentioned, assume quantity is 1.
10. Think step by step internally, but do not show the reasoning.
11. Keep the output machine-readable and deterministic.

```

### Example Inputs and JSON Outputs

#### Example 1

**Input:**

```text
2x Americano, 1 large fries and 3 hamburger
```
**Output:**

```json
{
  "items": [
    {"name": "Americano", "quantity": 2, "size": "regular"},
    {"name": "Fries", "quantity": 1, "size": "large"},
    {"name": "Hamburger", "quantity": 3, "size": "regular"}
  ],
  "total_items": 6
}
```

#### Example 2

**Input:**

```text
I'll have a cappuccino and two croissants please
```

**Output:**

```json
{
  "items": [
    {"name": "Cappuccino", "quantity": 1, "size": "regular"},
    {"name": "Croissant", "quantity": 2, "size": "regular"}
  ],
  "total_items": 3
}
```

#### Example 3

**Input:**

```text
Can I get three iced lattes, make one of them decaf
```

**Output:**

```json
{
  "items": [
    {"name": "Latte", "quantity": 2, "size": "regular", "options": ["iced"]},
    {"name": "Latte", "quantity": 1, "size": "regular", "options": ["iced", "decaf"]}
  ],
  "total_items": 3
}
```

#### Example 4

**Input:**

```text
One burger meal with coke, and an extra order of fries
```

**Output:**

```json
{
  "items": [
    {"name": "Burger", "quantity": 1, "size": "regular"},
    {"name": "Coke", "quantity": 1, "size": "regular"},
    {"name": "Fries", "quantity": 1, "size": "regular"}
  ],
  "total_items": 3
}
```

#### Example 5

**Input:**

```text
I would like two small cappuccinos, one large iced latte with oat milk, and a burger meal with coke.
```

**Output:**

```json
{
  "items": [
    {"name": "Cappuccino", "quantity": 2, "size": "small"},
    {"name": "Latte", "quantity": 1, "size": "large", "options": ["iced", "oat milk"]},
    {"name": "Burger", "quantity": 1, "size": "regular"},
    {"name": "Coke", "quantity": 1, "size": "regular"}
  ],
  "total_items": 5
}
```

### Security Note

The ChatGPT OpenAI API key is loaded from an environment variable or temporary `.env` file during runtime.

The real API key is not stored in this README, the notebook, `.env.example`, or the GitHub repository.
