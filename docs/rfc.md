# RFC: Component Ownership & Registry for flux-pipelines

## 1. Summary

We propose a **component registry + ownership model** for pipeline components used by `flux-pipelines`. Each component becomes:

- A **versioned unit** with clear metadata,
- Owned by a specific **maintainer** (domain expert),
- Validated by **automated checks** (env, size, tests),

with the long-term goal that `flux-pipelines` can fetch and use components by name and version.

We will pilot this with the **finetuning component** created by Kamil.

---

## 2. Motivation

Current issues:

- Components are maintained in an ad-hoc way; ownership is unclear.
- Non-experts often change components they don't fully understand.
- Quality and dependency hygiene vary across components.
- There is no central place to discover components, versions, and owners.

We want a **distributed but explicit** ownership model and a **single source of truth** for:

- What components exist,
- Which versions are available,
- Who is responsible for each component,
- Whether a component meets minimum quality standards.

---

## 3. Proposal

### 3.1 Ownership

Each component declares ownership in its metadata (e.g. `pyproject.toml`):

```toml
[tool.flux.component]
name = "finetuning"
owner = "kamil"            # GitHub or internal handle
owner_email = "..."
team = "ml-research"
```

**Owner responsibilities:**

- Keep the component buildable and testable (CI green).
- Maintain dependencies (no broken pins, avoid unnecessary bloat).
- Respond to issues and update requests.
- Maintain basic docs/usage examples.

### 3.2 Component Registry Repository

Create a **Component Registry** repo (inspired by Homebrew and Mason registries) that is the **source of truth** for:

- Component metadata (name, description, repo, path, owner, versions),
- Version history,
- CI checks required for acceptance.

Each component has a descriptor (e.g. TOML/YAML):

```toml
[component]
name = "finetuning"
description = "Fine-tuning component for model X"
repository = "https://github.com/physicsx/flux-components"
path = "components/finetuning"
version = "0.1.0"

[owner]
handle = "kamil"
team = "ml-research"
```

Implementation code can live in:

- A shared **components repo**, or
- Per-component repos referenced from the registry.

Registry CI is responsible for checking new or updated component versions.

### 3.3 Versioning

- Each component is **versioned independently** (e.g. `finetuning v0.1.0`).
- Prefer semantic versioning:
  - PATCH: bugfixes,
  - MINOR: backward-compatible features,
  - MAJOR: breaking changes.

The registry tracks all available versions.

---

## 4. Automated Checks

On each registry PR that adds or updates a component version, CI will:

1. **Dependency / env sanity**
   - Ensure `uv lock` succeeds for supported Python versions.

2. **Size constraints**
   - Build a `.venv` via `uv sync`; check max `.venv` size.
   - Optionally build a Docker image and enforce a max image size.

3. **Behavioural checks**
   - Run a basic smoke/unit test (import component, run simple config).
   - Optionally linting/type checks later.

4. **Metadata validation**
   - Ensure required fields exist:
     - `name`, `description`, `repository`, `path`, `version`,
     - `owner.handle` (+ optional `team`, contact).

If any of these fail, the component version is not accepted into the registry.

---

## 5. Workflow

**Adding / updating a component version:**

1. Component owner updates the component code in its repo and bumps its version.
2. Owner opens a PR in the **Component Registry** repo:
   - Adds/updates the descriptor with the new version.
   - Provides a short changelog/summary.
3. Registry CI runs all checks.
4. If green and reviewed, PR is merged and the version becomes **available**.

This:

- Makes responsibilities explicit (each component has an owner),
- Keeps a central, auditable history of component versions and quality checks,
- Distributes maintenance to domain experts.

---

## 6. Pilot: Finetuning Component

We will pilot this approach with the **finetuning component**:

- Currently has:
  - No unit tests,
  - No deployment via `flux-pipelines` templates,
  - No production usage yet (low risk).
- This makes it a good sandbox for refining:
  - Registry structure,
  - Metadata format,
  - CI checks,
  - Ownership conventions.

**Pilot steps:**

1. Add `[tool.flux.component]` ownership metadata to finetuning's `pyproject.toml`.
2. Create the **Component Registry** repo.
3. Add the finetuning descriptor as the first entry.
4. Implement minimal registry CI for:
   - `uv lock`,
   - `.venv` size check,
   - Docker image size check (if applicable),
   - A simple smoke test.
5. Use the registry-managed finetuning component for any client work (manually wired for now).
6. Iterate on structure, thresholds, and tests until stable.

Only after the pilot is stable do we design **formal integration** with `flux-pipelines` (e.g. `finetuning@0.1.0` resolution via the registry).

---

## 7. Open Questions

- What are sensible initial thresholds for:
  - `.venv` size,
  - Docker image size?
- Do we favour:
  - A single **components monorepo**, or
  - Multiple **per-component repos** referenced by the registry?
- How exactly should `flux-pipelines`:
  - Discover the registry,
  - Fetch and cache components,
  - Deal with incompatible versions?
- Do we need an explicit **deprecation policy** for old component versions?

---

## 8. Next Steps

- Agree on:
  - Ownership metadata format in `pyproject.toml`,
  - Descriptor format in the registry (TOML vs YAML).
- Create the **Component Registry** repo.
- Add the finetuning component as the first registry entry.
- Implement the first version of registry CI and iterate.
