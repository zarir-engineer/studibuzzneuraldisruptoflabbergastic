# 📦 Backend Scaffold for "Natural Language → Strategy Block" (FastAPI Version)

This backend uses **Python + FastAPI** to receive a natural language strategy string and return a JSON structure compatible with Algomin.

---

## 📁 File: `main.py`

```python
from fastapi import FastAPI, Request
from pydantic import BaseModel
import openai
import os

app = FastAPI()

# Set your OpenAI key here or use environment variable
openai.api_key = os.getenv("OPENAI_API_KEY")

class StrategyRequest(BaseModel):
    text: str

@app.post("/generate-strategy")
async def generate_strategy(request: StrategyRequest):
    prompt = f"""
Convert the following strategy to JSON with keys: id, type, operator, conditions.
Each condition includes left, operator, right.

Input: {request.text}
"""

    try:
        response = openai.ChatCompletion.create(
            model="gpt-4",
            messages=[
                {"role": "system", "content": "You are a trading strategy parser that returns JSON logic blocks."},
                {"role": "user", "content": prompt}
            ],
            temperature=0.3,
        )
        content = response['choices'][0]['message']['content']
        return {"result": content}
    except Exception as e:
        return {"error": str(e)}
```

---

## ▶️ To Run:

```bash
pip install fastapi uvicorn openai
export OPENAI_API_KEY=your_key_here
uvicorn main:app --reload
```

Your backend is now live at:  
👉 `http://localhost:8000/generate-strategy`

---

## ✅ Sample Input

```json
{
  "text": "If RSI < 30 and MACD crosses above signal line, buy."
}
```

## ✅ Sample Output (From AI)

```json
{
  "id": "block-123",
  "type": "condition",
  "operator": "and",
  "conditions": [
    { "left": "RSI", "operator": "<", "right": "30" },
    { "left": "MACD", "operator": "crossesAbove", "right": "Signal" }
  ]
}
```

---

## 📌 Notes

- You can validate `content` as JSON before returning it.
- Later, replace OpenAI with local models like `llama-cpp`, Ollama, or Groq.

Let me know if you want the **React hook + integration snippet** next.