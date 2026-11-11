---
name: deep-project-research
description: Use when researching a codebase, mapping its architecture or tracing an execution path.
---

# Deep Project Research

Read-only. Do not edit, install, commit or run destructive commands while researching.

## Order

1. **Entry points and manifests first.** Read `README`, `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, CI config, entry files and the top-level directory layout.
2. **Then the relevant area.** Trace only the modules, endpoints, tables or flows needed to answer the question.
3. **Do not read the whole repo by default on a large codebase.** Sample and follow dependencies from the entry points.
4. **Use tests and history only when needed.** Tests show expected behavior and failure modes. Git history helps with recent changes or refactors.

## Evidence

- Cite file paths for every claim. Add line numbers when useful.
- Separate "verified in code" from "inferred." If a conclusion comes from naming or convention rather than a read path, say so.
- Do not present a guess as a finding.

## Stopping condition

Stop when the question is answerable. Then state what was not explored and why. Do not keep reading for completeness.

## Synthesis

Summarize the purpose, architecture, dependencies and the flows relevant to the question. Keep it concise and direct.
