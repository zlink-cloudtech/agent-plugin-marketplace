# Copilot Instructions

## Project Overview

This repository serves as a marketplace catalog for Zlink Cloudtech's agent plugins. It uses a `marketplace.json` manifest file to declare available plugins that can be loaded by Claude AI and other compatible agent systems.

## Architecture

The repository follows a dual-location pattern:
- **Canonical source**: `.github/plugin/marketplace.json` - The authoritative plugin manifest
- **Symlink**: `.claude-plugin/marketplace.json` → symlinks to canonical source for Claude compatibility

This structure allows both GitHub Actions workflows and Claude's plugin discovery to access the same file.

## Manifest Schema

The `marketplace.json` file follows this structure:

```json
{
  "name": "marketplace-identifier",
  "metadata": {
    "description": "Marketplace description",
    "version": "semver"
  },
  "owner": {
    "name": "organization-name",
    "email": "contact@example.com"
  },
  "plugins": [
    {
      "name": "plugin-identifier",
      "source": {
        "source": "github",
        "repo": "owner/repo",
        "ref": "branch-or-tag"
      },
      "description": "Plugin description",
      "repository": "https://github.com/owner/repo.git",
      "license": "MIT"
    }
  ]
}
```

### Key Fields

- **plugins[].source**: Defines where the plugin code lives
  - `source`: Currently only "github" is supported
  - `repo`: GitHub repository in "owner/repo" format
  - `ref`: Git reference (branch, tag, or commit SHA)
- **plugins[].name**: Unique identifier for the plugin within this marketplace
- **plugins[].repository**: Full clone URL for the plugin repository
- **plugins[].license**: SPDX license identifier

## Conventions

### Plugin Naming
- Use kebab-case for plugin identifiers (e.g., `superpowers-dev`)
- Suffix development versions with `-dev` to distinguish from production releases
- Keep names short but descriptive of the plugin's purpose

### Version Management
- The marketplace itself has a `metadata.version` using semantic versioning
- Individual plugins are versioned through their Git refs (branches/tags)
- Use `ref: "main"` for development plugins, specific tags for stable releases

### File Organization
- All plugin manifests live in `.github/plugin/` directory
- Currently supports a single `marketplace.json` per repository
- Symlinks from `.claude-plugin/` maintain backward compatibility

## Adding New Plugins

When adding a new plugin to the marketplace:

1. Add a new entry to the `plugins` array in `.github/plugin/marketplace.json`
2. Ensure the plugin repository exists and is accessible
3. Verify the `ref` points to a valid branch/tag in the plugin repo
4. Bump the marketplace `metadata.version` following semver rules:
   - PATCH: Bug fixes, documentation updates
   - MINOR: New plugin additions
   - MAJOR: Breaking changes to manifest schema

## Common Patterns

### Development vs Production Plugins
- Development: Use `ref: "main"` and name suffix `-dev`
- Production: Use specific version tags like `ref: "v1.0.0"`

### Multi-Plugin Marketplace
When the marketplace grows, the `plugins` array will contain multiple entries. Each plugin operates independently, referencing its own repository.
