# Aster & Row — Reliable RAG Support Agent

A take‑home implementation for the AI Agent Intern assignment.

## Overview

This project implements a small, reliability‑first customer support agent that works over the supplied Aster & Row knowledge base and mock order data. Key features include:

- **Metadata‑aware retrieval** over `knowledge-base/*.md`
- **Policy precedence** handling with explicit supersession
- **Source citations** (filename + heading)
- **Safe abstention** when evidence is insufficient
- **Conflict resolution** for genuine active‑source contradictions
- **Order lookup tool** that returns only customer‑safe fields
- **Order‑ID normalization** and graceful handling of missing/unknown IDs
- **Multi‑turn session context** for follow‑up questions
- **Prompt‑injection resistance** for retrieved content
- **Privacy protection** for internal order fields
- **Structured debug traces**
- **Deterministic regression/evaluation tests**
- **Minimal Streamlit UI**

## Design Choices

- **Framework:** Python 3.10+ with Streamlit for the UI.
- **Retrieval:** A transparent local TF‑IDF‑style lexical retriever (`app/rag.py`). The corpus is small, so a deterministic in‑memory index avoids external dependencies and makes precedence and evaluation easy to inspect.
- **Generation / Agent behavior:** Core response policy is deterministic and grounded in retrieved company content. This makes high‑risk behaviours (policy precedence, tool calls, privacy, abstention, handoff) testable without relying on nondeterministic LLM outputs. The generation layer can be swapped for an LLM while preserving these contracts.
- **Storage:** In‑memory retrieval index and the supplied JSON order file; no production vector database is required.

## Setup

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

pip install -r requirements.txt
```

No API key is required.

## Running the CLI

```bash
python -m app.main            # normal mode
python -m app.main --debug    # with structured debug logs
```

## Running the Web UI

```bash
streamlit run app/streamlit_app.py
```

The UI displays the answer and an expandable debug section that shows retrieved passages, tool calls, sanitized tool results, and handoff state.

## Evaluation Suite

```bash
python evaluation/run_evaluation.py
```

The suite covers all 15 visible cases plus 7 original regression cases. It prints individual case results, category aggregates, and an overall summary.

## Tests

```bash
pytest -q
```

All tests pass:

```
22/22 cases passed (100.0%)
pytest: 12 passed
```

The evaluation command is deterministic and can be rerun from a clean checkout.
