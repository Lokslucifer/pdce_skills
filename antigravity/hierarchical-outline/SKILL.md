---
name: hierarchical-outline
description: Formats any document or artifact into a visual hierarchical outline (Title → Summary → Headings/Subsections → Nested Bullets) for rapid review, high information density, and zero verbose paragraphs. Use it during planning.
---

# Hierarchical Outline Skill

Summary: Enforces high-density, visually scannable document structures across all artifacts by replacing prose paragraphs with a strict hierarchy of titles, summaries, focused headings, and nested bullets.

## Structural Hierarchy

Document Skeleton:
- `# Title`: Top-level document identifier.
- `Summary`: 1–2 sentence distillation of the document's purpose and scope directly beneath the title.
- `## Headings`: Major logical domains, phases, or system boundaries for high-level visual navigation.
- `### Subheadings`: Fine-grained sub-topics requiring isolated reviewer focus.
- `Nested Bullets`: Core content placed under headings/subheadings (supports a maximum depth of 5 levels).

Nesting Rules:
- Maximum Depth Limit:
    - Allow nesting up to 5 levels deep; beyond 5 levels, decompose into a new subheading or separate section.
- One Idea Per Bullet:
    - Express exactly one requirement, architectural decision, action item, or constraint per bullet.
- Indented Sub-details:
    - Place rationale, technical specs, parameters, or edge cases beneath the parent bullet.
    - Prohibit multi-sentence narrative blocks; decompose paragraphs into nested bullets.
- Independent Top-Level Items:
    - Ensure every top-level bullet remains comprehensible without requiring immediate sub-bullet context.

## Visual Review & Density Rules

Scanning Optimization:
- Header-First Navigation:
    - Structure sections so reviewers can locate relevant details by scanning headings alone.
- High Signal-to-Noise:
    - Omit conversational preamble, repetitive introductory text, and filler conclusions.
    - Highlight identifiers, CLI commands, and parameters inline using backticks (e.g., `ainvoke()`, `asyncio.to_thread()`).
- Actionable Tasks & Criteria:
    - Format all actionable execution items and verification checks with task boxes (`- [ ]`).
- Code Blocks & Syntax Highlighting:
    - Place code snippets inside fenced markdown blocks with explicit language tags (e.g., ```` ```python ````, ```` ```bash ````, ```` ```yaml ````) for clean rendering.
    - Nest code blocks directly under the corresponding bullet or within dedicated code subsections.

## Reference Example

# Async RAG Migration Architecture

Summary: Architectural transition of repository and LLM orchestration layers to native asynchronous execution.

## Storage Layer

MongoDB Driver Migration:
- Replace synchronous `pymongo` with `motor.motor_asyncio`.
    - Connection pool max size: `50`.
    - Default timeout: `5000ms`.
- Convert repository operations to `async def`:
    - `find_document_by_id()`
    - `insert_chat_history()`
- Maintain identical query filters and return types.

## Orchestration Pipeline

Async Query Engine:
- Migrate LangChain chain execution:
    - Replace `.invoke()` calls with `.ainvoke()`.
    - Run vector search and document lookup concurrently via `asyncio.gather()`.
- Thread Offloading:
    - Wrap blocking CPU/disk operations with `asyncio.to_thread()`.

## Code Reference

Async Repository Implementation:
- Implementation snippet for asynchronous document retrieval:
    ```python
    from motor.motor_asyncio import AsyncIOMotorClient

    async def get_document(client: AsyncIOMotorClient, doc_id: str) -> dict | None:
        db = client["rag_db"]
        return await db["documents"].find_one({"_id": doc_id})
    ```

## Verification & Tasks

- [ ] Unit test Motor connection pool initialization.
- [ ] Benchmark concurrent retrieval under 20 simulated concurrent users.
- [ ] Validate regression test suite with `pytest -m async_engine`.

