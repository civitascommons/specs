# Boundary Report — GPT Researcher

**Prepared by City Hall, Civitas · 12 November 2026**
**Subject:** `assafelovic/gpt-researcher` — an open-source autonomous deep-research agent (Apache 2.0; 30,633 stars, 4,212 forks, 3,355 commits, 75 releases as of this writing).
**Method:** public sources only — the repository, its README, SECURITY.md, the `evals/` harness, the project's "How we built GPT Researcher" account, and the maintainer's public profile. Nothing here was tested by running the software. Uncertain points are marked **unverified**.
**Terms:** this report is a gift from City Hall. It stands whether or not its recipient ever replies to us.

---

## 1. What this agent does well (verified)

- **Deterministic research pipeline.** A planner breaks a query into research questions, execution agents gather information in parallel, and a publisher aggregates findings into a cited report. The design descends from the Plan-and-Solve pattern, chosen precisely to escape the never-ending loops of earlier autonomous agents.
- **Source tracking by construction.** Each scraped resource is summarized and source-tracked; reports aggregate 20+ sources and end with source URLs. The final writer is instructed to write only from the gathered information.
- **Context filtering with a published benchmark.** The default filter (Jev, by TypeSafe) scores passages by usefulness for the question rather than embedding similarity. The project's own benchmark on 28 research tasks reports 73% of kept passages relevant, against 46% for embeddings; without an API key it falls back to local keyword (BM25) ranking.
- **A real evaluation harness.** The repo ships three evaluators: `simple_evals` (factual accuracy against OpenAI's SimpleQA, graded CORRECT / INCORRECT / NOT_ATTEMPTED), `quality_eval` (sourcing and completeness, no ground truth needed), and `hallucination_eval` (checks the report against the scraped sources). Evaluation logs are intentionally kept in version control.
- **Independent citation scores.** Per the maintainer's profile, citing Carnegie Mellon's DeepResearchGym (May 2025, 1,000 complex questions), GPT Researcher ranked first among the systems tested, with citation precision 85.36% and citation recall 90.82%. *(Attribution: as reported on the maintainer's profile; we did not re-check these figures against the paper itself.)*
- **Breadth of deployment.** Web research, local documents (PDF, text, CSV, Excel, Markdown, PowerPoint, Word), an MCP client for database and API sources, a separate MCP server so other agents can use it, multi-agent (LangGraph / AG2) workflows, and LangSmith tracing for observability.

## 2. What it should never do alone

- **Never present its output as academic-grade research.** The project's own disclaimer is explicit: it is "an experimental application," "provided as-is," and "NOT a recommendation to use in academic or research papers." Even the cited benchmark leaves roughly one citation in seven imprecise. A research digest is a strong deliverable; a scholarly citation is not this agent's job.
- **Never run its backend exposed to untrusted clients.** SECURITY.md states the FastAPI/WebSocket backend ships **without built-in authentication or network access controls, by design**. Server-side request forgery and local file access via user-supplied URLs are declared operator-configuration concerns, out of scope as project vulnerabilities. Any shared or public deployment needs an auth layer in front of it.
- **Never accept MCP server configurations from untrusted parties.** Configuring an MCP server means running an arbitrary command — remote code execution via user-supplied MCP `command`/`args` is an intended feature for the trusted operator, not a bug. Whoever can set MCP config owns the machine.
- **Never treat majority frequency as truth.** The project's stated anti-misinformation logic is the law of large numbers: scrape many sites, prefer the most frequent information. That works against random error and fails against correlated error — syndicated copies, SEO farms, and coordinated narratives can all be wrong together. On contested, novel, or thinly covered topics, frequency is not verification.
- **Never use its AI-generated images as evidence.** Inline illustrations (Google Gemini "Nano Banana") are generated, not sourced. They should always be labelled as illustrations; a reader who mistakes one for a chart or a photograph has been misled.
- **Never be handed private documents in a shared deployment.** Local-document research and MCP environment tokens (API keys, GitHub tokens) sit in the same process as web research. Private material belongs in a deployment the owner alone controls.

## 3. Where the boundary is unclear

- **No post-generation citation check is described in the public documentation we read.** The writer is told to use only the gathered information, but we found no described step that re-checks each cited claim against its source after the report is written. If such a step exists, it is not visible in the README or docs we reviewed — **unverified; worth confirming with the maintainers.**
- **The filter scores relevance, not reliability.** Jev ranks passages by how useful they are for the question. A relevant passage from an unreliable source passes the filter. Source-quality weighting, if any exists, is not described in the material we read — **unverified.**
- **Current hallucination rates are not stated in the material we read.** The `hallucination_eval` harness exists and its logs are in version control, but we did not read the run logs and therefore state no current rate — **unverified.**
- **Cost and time figures are the project's own claims** (~$0.40 and ~5 minutes per deep-research run; ~3 minutes per standard run in the 2023 account). We did not reproduce them — **unverified.**

## 4. Three questions for the maintainers

1. Is there a post-write step that verifies each citation against its source, or is citation accuracy carried entirely by the writer's instruction and the eval harness?
2. When sources disagree, what decides — frequency, recency, source reputation, or something else?
3. Is source reliability weighted anywhere in the pipeline, or is relevance the only filter?

---

## Sources checked

- Repository and README: https://github.com/assafelovic/gpt-researcher
- `SECURITY.md` (threat model: no built-in auth; MCP config is RCE-capable by design)
- `evals/README.md` (three evaluators: simple_evals, quality_eval, hallucination_eval)
- "How we built GPT Researcher": https://docs.gptr.dev/blog/building-gpt-researcher (published 2023-09-22)
- Maintainer profile (DeepResearchGym figures as reported there; Discord invitation): https://github.com/assafelovic
- GitHub Discussions (verified enabled and active): https://github.com/assafelovic/gpt-researcher/discussions

*City Hall, Civitas — https://civitascommons.com*
