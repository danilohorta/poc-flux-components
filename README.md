# POC: flux-pipelines Component Registry

Proof-of-concept for a component registry and ownership model for `flux-pipelines` components.

## Overview

This repository implements the component registry system described in the RFC. It serves as a **single source of truth** for:

- Component metadata (name, description, version)
- Component ownership (maintainer, team)
- Component locations (repository, path)
- Version history and compatibility

## Structure

```
poc-flux-components/
├── registry/                    # Component descriptors
│   ├── finetuning.toml          # Example: finetuning component
│   └── ...
├── schemas/                     # Validation schemas
│   └── component.schema.json
├── .github/
│   └── workflows/
│       └── validate-registry.yml  # CI checks for component updates
└── README.md
```

## Component Descriptor Format

Each component is described by a TOML file in the `registry/` directory:

```toml
[component]
name = "finetuning"
description = "Fine-tuning component for model X"
repository = "https://github.com/physicsx/flux-components"
path = "components/finetuning"

[versions.latest]
version = "0.1.0"
image = "registry.io/physicsx/finetuning:0.1.0"
min_python = "3.10"
max_python = "3.13"

[owner]
handle = "kamil"
email = "kamil@physicsx.ai"
team = "ml-research"
team_id = "ee7d5858-1f72-4c1d-8ea4-b3bd18c6212c"

[quality]
has_tests = true
has_docs = true
max_venv_size_mb = 500
max_image_size_mb = 2048
```

## CI Validation

When a PR adds or updates a component descriptor, the CI pipeline automatically:

1. **Validates metadata**
   - Ensures all required fields are present
   - Checks TOML syntax
   - Validates version format (semver)

2. **Dependency checks** (planned)
   - Validates `uv lock` succeeds
   - Checks Python version compatibility

3. **Size constraints** (planned)
   - Measures `.venv` size after `uv sync`
   - Checks Docker image size (if applicable)

4. **Behavioral checks** (planned)
   - Runs smoke tests
   - Validates component can be imported

## Workflow

### Adding a new component

1. Create a TOML descriptor in `registry/{component-name}.toml`
2. Fill in all required metadata fields
3. Open a PR with the descriptor
4. CI validates the component
5. After review and approval, merge the PR

### Updating a component version

1. Update the component code in its repository
2. Bump the version in the component's `pyproject.toml`
3. Update the corresponding descriptor in this registry
4. Open a PR
5. CI validates the new version
6. After review, merge the PR

## Owner Responsibilities

Component owners are responsible for:

- Keeping the component buildable and testable (CI green)
- Maintaining dependencies (no broken pins, avoid bloat)
- Responding to issues and update requests
- Maintaining basic documentation and usage examples
- Updating the registry when releasing new versions

## Pilot: Finetuning Component

The **finetuning component** serves as the pilot for this system:

- Currently has no unit tests
- Not deployed via flux-pipelines templates
- No production usage (low risk)

This makes it ideal for:
- Refining registry structure
- Testing metadata formats
- Developing CI checks
- Establishing ownership conventions

## Integration with flux-pipelines (Future)

Once stable, `flux-pipelines` will:

- Discover components via this registry
- Fetch components by name and version (e.g., `finetuning@0.1.0`)
- Cache components locally
- Handle version compatibility

## Related Documentation

- [RFC: Component Ownership & Registry](docs/rfc.md) (full proposal)
- [Metadata Schema](schemas/component.schema.json)
- [CI Validation Process](.github/workflows/validate-registry.yml)
