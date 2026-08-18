# Artifact Contract

## Purpose

This repository follows an artifact-first distribution model.

## Contract

1. The implementation source remains in the plugin source repository.
2. The marketplace repository only contains generated artifacts and metadata.
3. The build step is the boundary between source code and publishable output.
4. The marketplace index references the published artifact, not the original source tree.

## Allowed contents

- marketplace catalog entries
- build metadata
- generated plugin artifacts
- tags / release refs
- installable package manifests

## Artifact layout

Generated plugin artifacts live under:

```text
artifacts/<plugin-name>/
```

Each artifact should include a platform-native install surface plus `build-meta.json`. For Claude-compatible plugins, the artifact root must include `.claude-plugin/plugin.json`.

## Forbidden contents

- primary business logic implementation
- authoritative source code for plugin features
- ungenerated source directories intended as the canonical implementation

## Release process

```text
plugin-source -> validate -> build -> publish artifact -> register in marketplace -> consumer install
```

## Operational rule

When changes are needed, edit the source repo first and then regenerate the artifact for release. The marketplace repo should only reflect the shipped output.
