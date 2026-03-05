```markdown
# AI Evaluation Metrics Spike

**Status:** Completed  
**Audience:** IT & Technical Stakeholders

---

## Background & Problem

When deploying an AI system, we need confidence that it is producing correct, high-quality responses. Manually reading a handful of answers is not scalable — we need an automated, reliable way to score AI responses.

This investigation explores which automated scoring method is best at identifying whether an AI response is correct, by comparing it against a known correct answer (called a **"ground truth"**).

> Think of it like automated test marking: rather than a person checking every exam paper, a marking system compares the answer to the answer sheet. The question is — which marking system is most reliable?

---

## What Is a "Ground Truth"?

A ground truth is a confirmed correct answer used as a benchmark. In this context:

- **Question answering** — a dataset of questions where the correct answers are already known
- **Document summarisation** — a dataset of documents paired with human-written summaries

The scoring method must reliably distinguish between an AI response that matches the ground truth and one that does not.

---

## Scoring Methods Tested

Five automated scoring methods were compared. Each takes an AI-generated response and a ground truth, and outputs a score reflecting how correct or similar they are.

| Method | What It Does | How It Works | Best For | Analogy |
|---|---|---|---|---|
| **DeepEval** | Uses an AI model to judge another AI's response | Sends both responses to a 'judge' AI which scores the answer | Complex questions where meaning matters | A senior expert reviewing someone else's work |
| **BERTScore** | Measures how similar the meaning of two texts is | Converts text to numbers and compares mathematical similarity | Checking meaning matches, even with different wording | Checking if two sentences say the same thing differently |
| **Pydantic** | Checks whether the AI's response matches expected structure and content | Uses strict rules to validate format and correctness | Structured data, Q&A, and summaries | A form validator checking all required fields are correct |
| **RAGAS** | A specialist toolkit designed specifically for RAG AI systems | Combines multiple scoring methods built for AI retrieval systems | End-to-end RAG pipeline evaluation | A purpose-built inspection checklist for AI search systems |
| **METEOR** | Compares text using word matching, synonyms and paraphrasing | Looks for matching words and close variants between two texts | Text similarity where synonyms are acceptable | A sophisticated spell/synonym checker |

---

## How We Tested Them

### Test 1: Question Answering

We used a public dataset of questions, each with known correct and incorrect answers. Every answer was fed into each scoring method. We measured:

- **Score separation** — how far apart the scores were for correct vs. incorrect answers (bigger gap = better)
- **Classification accuracy** — how accurately each method labelled an answer as correct or incorrect, measured using **F1 score** (a standard accuracy metric where 1.0 is perfect)

We also tested different AI models within each method and different **temperature** settings. Temperature controls how creative/random an AI is — a value of 0 means the AI always gives the most consistent, predictable answer.

### Test 2: Document Summarisation

We used a public dataset of documents with human-written summaries. A set of "fake" summaries was created by randomly reassigning summaries to the wrong documents. Each scoring method rated both the real and fake summaries.

We measured the same things as Test 1: score separation and F1 accuracy.

> **Note:** Setting the classification threshold at the midpoint between average correct and incorrect scores improved accuracy across all methods.

---

## Results

### Question Answering

| Method | Separates Correct vs Incorrect? | Accuracy (F1 Score) | Best AI Model |
|---|---|---|---|
| **Pydantic** | ✅ Best separation | Good | GPT OSS 120b |
| **DeepEval** | ✅ Good separation | ⭐ Best F1 score | GPT OSS 120b |
| BERTScore | Moderate | Moderate | Varies |
| RAGAS | Moderate | Moderate | Varies |
| METEOR | Weaker separation | Lower | Varies |

### Document Summarisation

| Method | Separates Correct vs Incorrect? | Accuracy (F1 Score) | Best AI Model |
|---|---|---|---|
| **Pydantic** | ✅ Best separation | ⭐ Best F1 score | Claude 3 Haiku |
| **DeepEval** | ✅ Good separation | Good | Claude 3 Haiku |
| BERTScore | Moderate | Moderate | Varies |
| RAGAS | Moderate | Moderate | Varies |
| METEOR | Weaker separation | Lower | Varies |

Temperature had minimal impact on Q&A results. For summarisation, a slight improvement was observed at temperature 0.1, though the difference was small.

---

## Recommendation

Pydantic is the recommended scoring method — it achieved the best separation between correct and incorrect responses across both test types. The optimal AI model differs by task:

| Use Case | Recommended Method | Recommended AI Model | Temperature Setting |
|---|---|---|---|
| Checking Q&A correctness | Pydantic | GPT OSS 120b | 0 (off) |
| Checking document summaries | Pydantic | Claude 3 Haiku | 0 (off) |

> ⚠️ **Important caveat:** These conclusions are based on a small sample (30 prompts per combination). Results should be treated as indicative, not definitive, until a larger-scale run is completed.

---

## Next Steps & Limitations

### Current Limitation

Testing was performed on a standard laptop, limiting the volume of data processed. With only 30 prompts per method/model/temperature combination, there is a risk results are influenced by chance.

### Recommended Actions

- Re-run the full experiment on a compute cluster
- Increase to a minimum of **100 prompts** per combination
- Confirm **Pydantic + GPT OSS 120b** and **Pydantic + Claude 3 Haiku** as the final configuration

---

## Glossary

| Term | Plain English Explanation |
|---|---|
| **LLM** | Large Language Model — the AI system being tested (e.g. GPT, Claude) |
| **RAG** | Retrieval Augmented Generation — an AI technique that fetches relevant documents before generating an answer |
| **Ground Truth** | The confirmed correct answer used as a benchmark for comparison |
| **F1 Score** | A standard measure of classification accuracy (0 = useless, 1.0 = perfect) |
| **Temperature** | A setting controlling how random/creative an AI's responses are (0 = consistent, higher = more varied) |
| **Semantic Similarity** | How similar the meaning of two pieces of text is, regardless of exact wording |
| **Evaluation Metric** | An automated method for scoring AI response quality |
| **Confusion Matrix** | A table showing how often a classifier was correct, incorrect, or uncertain |
```
