# Component Registry

This directory contains TOML descriptor files for all registered flux-pipelines components.

## Structure

Each component has its own TOML file named after the component:

```
registry/
├── finetuning.toml       # Finetuning component (pilot)
├── preprocessing.toml     # Example: preprocessing component
└── evaluation.toml        # Example: evaluation component
```

## File Format

See [docs/CONTRIBUTING.md](../docs/CONTRIBUTING.md) for the complete descriptor format and field reference.

## Adding a Component

To register a new component:

1. Create `registry/<component-name>.toml`
2. Fill in all required fields
3. Submit a PR
4. CI will validate your descriptor
5. After review and approval, your component is registered

## Current Components

| Component | Version | Owner | Status |
|-----------|---------|-------|--------|
| [finetuning](finetuning.toml) | 0.1.0 | kamil | Pilot |

## Validation

All descriptors are automatically validated on PR:

- TOML syntax
- Required fields
- Semantic versioning
- Field formats

See [.github/workflows/validate-registry.yml](../.github/workflows/validate-registry.yml) for details.
