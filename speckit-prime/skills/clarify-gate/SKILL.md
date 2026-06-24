---
name: clarify-gate
description: >
  Decision gate for handling open questions autonomously. Classify every
  ambiguity as mechanical (resolve yourself) or material (route to CEO).
  Never ask the human directly — only the CEO does that.
---

# Clarify Gate

Use this skill whenever you encounter an open question, missing information, or an ambiguity that could block progress.

## Classification

**Mechanical** — resolve yourself, document the assumption:
- A sane default exists for this type of project
- The blast radius is low (reversible, local to one artifact section)
- Industry-standard practice covers it
- The answer can be inferred from the spec, constitution, or project context

**Material** — route to the CEO, do NOT resolve yourself:
- The choice changes feature scope
- The choice contradicts or is not covered by the project constitution
- Multiple reasonable interpretations exist with meaningfully different outcomes
- The decision requires product ownership (data policy, security posture, external credentials, pricing)
- You have already tried to infer it and cannot produce a defensible default

## How to handle each type

### Mechanical
1. Pick the most conservative, reversible default
2. Write a one-line assumption note inline in the artifact: `[ASSUMPTION: <what you decided and why>]`
3. Continue without interrupting the pipeline

### Material
1. Do NOT ask the user yourself
2. Stop work on the affected artifact
3. Route to the CEO with:
   - The exact question
   - Your recommended default and its implications
   - What will be blocked until the decision is made
4. Let the CEO batch it with other pending questions before surfacing to the human

## Never ask about (these are safe mechanical defaults)

- Data retention (use industry standard for the domain)
- Performance targets (use standard web/mobile expectations unless specified)
- Error handling style (user-friendly messages with fallbacks)
- Integration patterns (use what the project already uses)

## Always route to CEO (these are tech-stack decisions requiring human input)

- Authentication method (OAuth2 vs session vs API key — changes architecture)
- Database type or product (Postgres vs MongoDB vs Redis etc.)
- Framework choice (FastAPI vs Django vs Express vs Next.js etc.)
- Primary language (Python vs TypeScript vs Go etc.)
- Hosting / deployment target (Docker, serverless, on-prem)
- AI/ML components (LLM provider, embedding model, inference runtime)
- Any decision that introduces a new dependency, service, or infrastructure component
