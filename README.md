# Aster & Row — Reliable RAG Support Agent

A take‑home implementation for the AI Agent Intern assignment.

## Overview

This project implements a small, reliability‑first customer support agent that works over the supplied Aster & Row knowledge base and mock order data. Key features include:

- Metadata‑aware retrieval from `knowledge-base/*.md`
- Active/official policy precedence with supersession handling
- Source citations (filename + heading)
- Safe abstention when evidence is insufficient
- Explicit handling of genuine active‑source conflicts
- An order‑lookup tool that returns only customer‑safe fields
- Order‑ID normalization and graceful handling of missing/unknown IDs
- Multi‑turn session context for follow‑up questions
- Prompt‑injection resistance for retrieved content
- Privacy protection for internal order fields
- Structured debug traces
- Deterministic regression/evaluation tests
- A minimal Streamlit UI

## Design Choices

- **Framework:** Python 3.10+ with Streamlit.
- **Retrieval:** A transparent local TF‑IDF‑style lexical retriever (`app/rag.py`). The corpus is small, so a deterministic in‑memory index avoids external dependencies and simplifies inspection of precedence and evaluation.
- **Generation/Agent Behavior:** Core response policy is deterministic and grounded in retrieved company content. This makes high‑risk behaviours (policy precedence, tool calls, privacy, abstention, handoff) testable without relying on nondeterministic LLM output. The generation boundary can be swapped for an LLM while preserving these contracts.
- **Storage:** In‑memory index and supplied JSON order file; no production vector database required.

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
python -m app.main --debug    # enable structured debug logs
```

## Running the Web UI

```bash
streamlit run app/streamlit_app.py
```

The UI displays the answer and an expandable debug panel that shows retrieved passages, tool calls, sanitized tool results, and handoff state.

## Evaluation Suite

```bash
python evaluation/run_evaluation.py
```

The suite covers all 15 visible cases plus 7 original regression cases. It prints per‑case results, category aggregates, and an overall summary.

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
