# Aster & Row — Reliable RAG Support Agent

Take-home implementation for the AI Agent Intern assignment.

## What this builds

A small, reliability-first customer support agent over the supplied Aster & Row
knowledge base and mock order data.

It provides:

- metadata-aware retrieval over `knowledge-base/*.md`
- active/official policy precedence and supersession handling
- source citations (filename + heading)
- safe abstention when evidence is insufficient
- explicit handling of genuine active-source conflicts
- an order lookup tool that returns only customer-safe fields
- order-ID normalization and safe handling of missing/unknown IDs
- multi-turn session context for follow-up questions
- prompt-injection resistance for retrieved content
- privacy protection for internal order fields
- structured debug traces
- deterministic regression/evaluation tests
- a minimal Streamlit interface

## Design choices

**Framework:** Python + Streamlit.

**Retrieval:** a transparent local TF-IDF-style lexical retriever implemented in
`app/rag.py`. The supplied corpus is small, so a local deterministic index avoids
unnecessary infrastructure and makes precedence and evaluation easy to inspect.

**Generation/agent behavior:** the core response policy is deterministic and
grounded in retrieved company content. This is intentional for the take-home:
the highest-risk behaviors (policy precedence, tool calls, privacy, abstention,
and handoff) should be testable without depending on another LLM's nondeterminism.
The architecture leaves the generation boundary replaceable by an LLM while
keeping these contracts in application code.

**Storage:** in-memory retrieval index and the supplied JSON order file. No
production vector database is needed.

## Setup

Requires Python 3.10+.

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

pip install -r requirements.txt
```

No API key is required.

## Run the CLI

From the repository root:

```bash
python -m app.main
```

For structured debug logs:

```bash
python -m app.main --debug
```

## Run the web UI

```bash
streamlit run app/streamlit_app.py
```

The UI shows the answer, and the debug expander exposes retrieved passages,
tool calls, sanitized tool results, and handoff state.

## Run the evaluation suite

```bash
python evaluation/run_evaluation.py
```

The suite covers all 15 supplied visible cases plus 7 original cases. It prints
individual case results, category results, and an overall result.

## Tests

```bash
pytest -q
```

## Evaluation results

### Baseline

A deliberately simple baseline (single-pass lexical retrieval without explicit
policy precedence, order-tool privacy filtering, conflict handling, or
conversation state) was evaluated conceptually against the visible behavior
requirements. It is included as an engineering baseline rather than a claim of
an external model benchmark:

- Retrieval / precedence: 2/5
- Groundedness / abstention: 2/3
- Tool use / data handling: 2/4
- Privacy / security: 0/2
- Conversation: 0/1
- Source conflicts: 0/1

### Final

Local verified run on the supplied corpus, all 15 visible cases, and 7 original
regression cases:

```text
22/22 cases passed (100.0%)
pytest: 12 passed
```

The evaluation command is deterministic and can be rerun from a clean checkout.
