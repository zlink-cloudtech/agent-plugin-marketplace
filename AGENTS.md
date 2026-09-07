# agent-plugin-marketplace

Marketplace and release artifact registry for Claude-compatible plugins.

## Role

This repository is not the implementation home for plugin logic. It is the consumer-facing distribution layer that exposes installable plugin artifacts and catalog metadata.

The actual source of truth for plugin behavior, schema, build logic, and validation lives in upstream repos such as `agent-plugins`, `superpowers`, and similar plugin authoring repositories.

## What this repo owns

- `marketplace.json`: marketplace catalog and plugin source references
- `.claude-plugin/marketplace.json`: Claude-compatible entrypoint used by plugin consumers
- `artifacts/<plugin-name>/`: generated installable artifact directories
- release metadata and artifact indexing for public installation

See [README.md](README.md) and [marketplace.json](marketplace.json) for the repo's canonical overview and active catalog entries.

## Non-goals

Do not treat this repository as the canonical source for business logic, runtime behavior, or skill implementation.

If a task requires:

- changing plugin logic,
- fixing prompt/skill behavior,
- modifying source code in a plugin,
- updating plugin schema or validation rules,

then the correct action is to work in the upstream source repository, rebuild the artifact, and then update this repo's marketplace metadata or artifact reference.

## Working rules for agents

1. Prefer release and catalog work in this repo.
   - update marketplace entries
   - update artifact metadata or version refs
   - add/remove published plugin entries
   - fix installation or indexing metadata

2. Do not create implementation code here unless the task is explicitly about the distribution layer itself.

3. Keep artifacts in `artifacts/` as generated output, not as hand-authored source code.

4. Preserve stable plugin names, URLs, refs, and strict install semantics when editing catalog data.

5. When a user asks for a plugin feature or bug fix, first confirm whether it belongs in the source repo rather than this marketplace repo.

## Typical tasks

- add a new published plugin artifact
- update the source URL or ref for an existing plugin
- refresh artifact metadata after a source repo release
- remove stale or invalid marketplace entries
- fix the local Claude-compatible marketplace index

## Release flow

```text
source repo -> build artifact -> publish under artifacts/ -> register in marketplace.json -> Claude installs from catalog
```

## Boundaries

This repo is intentionally the packaging and discovery layer, not the implementation layer. A plugin bug or feature change belongs in its source repository; a catalog or release issue belongs here.
