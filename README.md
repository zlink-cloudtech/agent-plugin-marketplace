# Zlink Cloudtech Agent Plugins

A marketplace for installing ready-to-use AI agent plugins in both Claude Code and GitHub Copilot CLI.

This repository is the consumer-facing catalog for plugin distribution. It lets you add a marketplace and install published plugins with the standard plugin commands used by the tools.

## Why use this marketplace?

If you want to quickly install reusable workflow plugins such as the PRD workflow, this repo gives you a single catalog entry point.

Typical usage:

```bash
claude plugin marketplace add zlink-cloudtech/agent-plugin-marketplace
claude plugin install agent-plugins@zlink-cloudtech-agent-plugins
```

```bash
copilot plugin marketplace add zlink-cloudtech/agent-plugin-marketplace
copilot plugin install agent-plugins@zlink-cloudtech-agent-plugins
```

## Quick start

### 1) Add the marketplace

Add this repository as a plugin marketplace so the CLI can discover the published plugins.

```bash
# Claude Code
claude plugin marketplace add zlink-cloudtech/agent-plugin-marketplace

# GitHub Copilot CLI
copilot plugin marketplace add zlink-cloudtech/agent-plugin-marketplace
```

You can also use a Git URL or local path if needed:

```bash
claude plugin marketplace add https://github.com/zlink-cloudtech/agent-plugin-marketplace.git
copilot plugin marketplace add https://github.com/zlink-cloudtech/agent-plugin-marketplace.git
```

### 2) Browse the catalog

```bash
claude plugin marketplace browse zlink-cloudtech-agent-plugins
copilot plugin marketplace browse zlink-cloudtech-agent-plugins
```

### 3) Install a plugin

```bash
# Claude Code
claude plugin install agent-plugins@zlink-cloudtech-agent-plugins

# GitHub Copilot CLI
copilot plugin install agent-plugins@zlink-cloudtech-agent-plugins
```

The plugin name is `agent-plugins`, and the marketplace name is `zlink-cloudtech-agent-plugins`.

## Available plugins

This marketplace currently publishes plugin artifacts for the following workflows:

- `agent-plugins` — PRD workflow + system design skills (requirements clarification, PRD quality review, technical research, and multi-service technical specification)
- `superpowers-dev` — development build of the Superpowers plugin
- `agent-plugin-cli-dev` — development build of the CLI plugin

## Marketplace layout

```text
.github/plugin/marketplace.json
.claude-plugin/marketplace.json
plugins/
  agent-plugins/
```

The `marketplace.json` files define the catalog metadata and plugin source mappings that clients consume when they add the marketplace and install plugins.

## How this repo fits into the plugin workflow

This repository is the distribution layer, not the source of truth for plugin logic.

- plugin implementation lives in upstream source repositories
- build artifacts are published here
- the marketplace index exposes them to Claude and Copilot
- users install only the generated artifact surface

## Related projects

- [agent-plugins](https://github.com/zlink-cloudtech/agent-plugins)
- [superpowers](https://github.com/zlink-cloudtech/superpowers)
- [agent-plugin-cli](https://github.com/zlink-cloudtech/agent-plugin-cli)

## License

This repository serves as a plugin marketplace and distribution catalog. Individual plugin packages may carry their own licenses, so please check the source repository for the specific plugin you install.

## Support

If you are looking for installation help or want to publish a new plugin to this catalog, use the upstream source repositories and then refresh the marketplace entry with the generated artifact.
