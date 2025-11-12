# Contributing to the Component Registry

Thank you for contributing to the flux-pipelines component registry! This guide will help you add or update component entries.

## Adding a New Component

### Prerequisites

1. Your component should exist in a Git repository
2. It should have a `pyproject.toml` with version information
3. You should be the designated owner or have approval from the component owner

### Steps

1. **Fork and clone this repository**
   ```bash
   git clone https://github.com/<your-username>/poc-flux-components.git
   cd poc-flux-components
   ```

2. **Create a new branch**
   ```bash
   git checkout -b add-component-<component-name>
   ```

3. **Create the component descriptor**

   Create a new file `registry/<component-name>.toml` with the following structure:

   ```toml
   [component]
   name = "your_component"
   description = "Brief description of what your component does"
   repository = "https://github.com/your-org/your-repo"
   path = "components/your_component"

   [versions.latest]
   version = "0.1.0"
   image = "registry.io/org/component:0.1.0"
   min_python = "3.10"
   max_python = "3.13"
   released = "2025-11-12"

   [owner]
   handle = "your-github-username"
   email = "your.email@example.com"
   team = "your-team"
   team_id = "team-uuid-if-applicable"

   [quality]
   has_tests = true
   has_docs = true
   max_venv_size_mb = 500
   max_image_size_mb = 2048

   [metadata]
   tags = ["tag1", "tag2"]
   requires_gpu = false
   experimental = false
   ```

4. **Validate locally** (optional)

   ```bash
   python -c "import toml; toml.load('registry/<component-name>.toml')"
   ```

5. **Commit and push**
   ```bash
   git add registry/<component-name>.toml
   git commit -m "Add <component-name> component to registry"
   git push origin add-component-<component-name>
   ```

6. **Create a Pull Request**
   - Go to the repository on GitHub
   - Click "New Pull Request"
   - Select your branch
   - Fill in the PR template with:
     - Component name and version
     - Brief description of the component
     - Link to the component repository
     - Any special requirements or notes

7. **Wait for CI validation**
   - The CI pipeline will validate:
     - TOML syntax
     - Required fields
     - Version format (semantic versioning)
   - Fix any issues reported by CI

8. **Address review feedback**
   - A maintainer will review your PR
   - Make any requested changes
   - Push updates to your branch

9. **Merge**
   - Once approved and CI passes, your PR will be merged
   - Your component is now in the registry!

## Updating an Existing Component

### When to Update

- New version released
- Metadata changes (owner, team, description)
- Quality improvements (added tests, documentation)
- Configuration changes (size limits, Python versions)

### Steps

1. **Fork and clone** (if you haven't already)

2. **Create a branch**
   ```bash
   git checkout -b update-component-<component-name>
   ```

3. **Update the descriptor**

   Edit `registry/<component-name>.toml`:

   - For a new version, you can either:
     - Update `[versions.latest]` with the new version details
     - Add a new version section `[versions.v0_2_0]` and update `latest`

   Example:
   ```toml
   [versions.latest]
   version = "0.2.0"
   image = "registry.io/org/component:0.2.0"
   released = "2025-11-15"

   [versions.v0_1_0]
   version = "0.1.0"
   image = "registry.io/org/component:0.1.0"
   released = "2025-11-12"
   deprecated = false
   ```

4. **Commit, push, and create PR** (same as steps 5-9 above)

## Descriptor Field Reference

### Required Fields

| Section | Field | Type | Description |
|---------|-------|------|-------------|
| component | name | string | Component identifier (lowercase, alphanumeric, underscores) |
| component | description | string | Brief description (10-200 chars) |
| component | repository | string | Git repository URL |
| component | path | string | Path to component in repository |
| versions.latest | version | string | Semantic version (MAJOR.MINOR.PATCH) |
| versions.latest | image | string | Container image reference |
| owner | handle | string | GitHub username or handle |
| owner | team | string | Team name |

### Optional Fields

| Section | Field | Type | Description |
|---------|-------|------|-------------|
| versions.latest | min_python | string | Minimum Python version (e.g., "3.10") |
| versions.latest | max_python | string | Maximum Python version (e.g., "3.13") |
| versions.latest | released | string | Release date (YYYY-MM-DD) |
| versions.latest | deprecated | boolean | Whether version is deprecated |
| owner | email | string | Owner email address |
| owner | team_id | string | Linear team UUID |
| quality | has_tests | boolean | Component has tests |
| quality | has_docs | boolean | Component has documentation |
| quality | max_venv_size_mb | number | Max .venv size in MB |
| quality | max_image_size_mb | number | Max image size in MB |
| metadata | tags | array | Tags for discovery |
| metadata | requires_gpu | boolean | Requires GPU |
| metadata | experimental | boolean | Experimental status |

## Validation Rules

### Component Name

- Must be lowercase
- Can contain letters, numbers, and underscores
- Must start with a letter
- Example: `finetuning`, `data_processor`, `model_evaluator`

### Version Format

- Must follow semantic versioning: `MAJOR.MINOR.PATCH`
- Examples: `0.1.0`, `1.2.3`, `2.0.0`

### Python Versions

- Must be one of: `3.10`, `3.11`, `3.12`, `3.13`

## CI Checks

The following checks run automatically on every PR:

1. **TOML Syntax Validation**
   - Ensures the file is valid TOML
   - Checks for syntax errors

2. **Required Fields Check**
   - Verifies all required fields are present
   - Ensures proper nesting structure

3. **Semantic Version Validation**
   - Checks version format matches `X.Y.Z`
   - Ensures version follows semver conventions

### Future Checks (Planned)

- Dependency validation via `uv lock`
- `.venv` size measurement
- Docker image size check
- Component import smoke test

## Getting Help

- **Issues**: Open an issue in this repository
- **Questions**: Ask in the #flux-pipelines channel
- **RFC Discussion**: See [docs/rfc.md](rfc.md)

## Code of Conduct

- Be respectful and professional
- Focus on technical merit
- Help maintain quality standards
- Document your changes clearly
