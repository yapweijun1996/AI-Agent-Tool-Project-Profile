# agent-project-profile

**Repo Intelligence Foundation for the AI Agent Tools ecosystem.**

A deterministic repository profiler that tells an AI coding agent how a repository is structured and how the repository declares it should be operated, without running it.

## Project status

**V1 implementation is committed and published; full cross-platform completion is pending.** The standalone npm package is version `0.1.1`, the executable JSON Schema is [schema/profile.schema.json](schema/profile.schema.json), and the frozen synthetic fixture suite covers the required 16 categories plus boundary tests. Windows and Linux verification pass; macOS is unverified. The package is published on [npm](https://www.npmjs.com/package/agent-project-profile) under the `latest` tag. Version `0.1.1` is the corrective patch for the published package README.

The Company KB `AI Agent Tools` (`ai-agent-tools`) remains the project knowledge SSOT. Its roadmap snapshot still records roadmap #3 as `next` after standalone `agent-change-impact`; the owner explicitly authorized this implementation task without changing that ecosystem roadmap state. The tool status record is maintained separately from the ecosystem roadmap and must not be read as proof of cross-platform verification or ecosystem roadmap activation.

The implementation freezes the local V1 choices: Node `>=18.18.0`, no runtime dependencies, bounded inert metadata parsing, the budgets in [SPEC.md](SPEC.md), stable evidence IDs, and the CLI contract below. See [DECISIONS.md](DECISIONS.md) for provenance and lifecycle decisions, and [ROADMAP.md](ROADMAP.md) for verification and release status.

## Purpose

Agents repeatedly inspect manifests, lockfiles, scripts, workspace declarations, instructions, and CI files before they can work on a repository. This tool turns that discovery into a bounded, machine-readable fact profile.

It is a metadata-oriented scanner. It does not understand the entire codebase and does not summarize source code.

## V1 principles

- Local-first, read-only, deterministic, JSON-first, and cross-platform.
- No LLM, API key, network access, package installation, or project command execution.
- Evidence-backed facts with explicit unknowns and stable diagnostics.
- Bounded scanning and output, including large workspaces.
- Standalone operation without a hosted backend.
- Fail-closed behavior when evidence is missing, conflicting, or unreadable.

**A declared verification command is not a proven safe command.** Discovering a `test` script means the project declares that script. It does not mean the script is safe, succeeds, or even runs in the current environment. Every reported command has `execution: "not_run"`; the profile never contains a `safe: true` assertion.

## Support

| Area | V1 behavior |
| --- | --- |
| Generic repositories | Repository markers, structure signals, instruction files, CI/config inventory, lockfiles, ecosystem signals |
| JavaScript / TypeScript / Node.js | First-class manifest, package manager, runtime declaration, workspace, script, and declared entrypoint profiling |
| npm / pnpm / Yarn | Declaration and lockfile detection; manifest-based workspace discovery |
| Python / Go / Rust / Java | Sentinel-based detection only; no commands or runtime details inferred |

Configuration discovery includes TypeScript, Vite, Vitest, Jest, ESLint, Prettier, Turbo, Nx, webpack, and Rollup. Finding a configuration file does not prove that the project uses it. Browser-related configuration is inventoried without inventing a browser runtime requirement.

## CLI

The examples and exact exit/strict-mode rules below describe the delivered `0.1.1` contract. The package is published on npm; the schema and representative profiles remain versioned repository artifacts.

```sh
agent-project-profile .
agent-project-profile . --format json
agent-project-profile . --format json --pretty
agent-project-profile . --format text
agent-project-profile . --strict
```

JSON is the default. Text output represents the same normalized facts. The profiler writes the report to stdout and human-readable diagnostics to stderr; a caller can explicitly save the output:

```sh
agent-project-profile . > project-profile.json
```

Shell redirection is a caller-controlled write. The profiler itself does not create a report file, cache, temporary file, or modify the inspected repository.

| Exit code | Meaning |
| --- | --- |
| `0` | Complete, usable profile; strict mode has no warning/error diagnostics |
| `1` | Fatal failure; no usable profile |
| `2` | Partial or unsupported profile, or strict-mode warning/error diagnostic |

Package manager ambiguity emits a profile with `status: "partial"` and exits `2`, including without `--strict`. An unsupported-only sentinel profile uses `status: "unsupported"` and also exits `2`.

## Output

The versioned JSON contract has these top-level fields:

`schemaVersion`, `toolVersion`, `status`, `project`, `ecosystems`, `packageManager`, `runtimes`, `workspace`, `commands`, `scripts`, `entrypoints`, `configs`, `instructions`, `ci`, `evidence`, `warnings`, and `coverage`.

The canonical names are `runtimes` and `workspace`. The early brief's `runtime` and `workspaces` spellings are not aliases. `coverage` records scan/output limits so consumers can distinguish an absent fact from incomplete discovery.

See [SPEC.md](SPEC.md) for field semantics, evidence references, unknown states, status rules, and security boundaries. The schema is a delivered artifact, while semantic reference and coverage checks are exercised by the test suite.

Schema-valid representative profiles for `complete`, `partial`, `unsupported`, and `error` states are in [examples/](examples/).

## Ecosystem responsibilities

Each capability has its own repository and npm package, with independent versions, tests, and releases. The separate `AI-Agent-Tools` hub owns registry, standards, discovery, documentation, compatibility, and roadmap artifacts; the Company KB records the authoritative project knowledge and decisions. Codex/Claude/AGRUN own reasoning and orchestration. A monorepo or shared core is deferred until roughly 3-5 mature tools demonstrate real duplicated infrastructure.

| Tool | Responsibility |
| --- | --- |
| `agent-project-profile` | Repository metadata facts and declared operating commands |
| `agent-code-slice` | Relevant source-code slices |
| `agent-change-impact` | Change impact analysis |
| `agent-test-scope` | Test selection using profile and change evidence |
| `agent-error-lens` | Error interpretation |
| `agent-patch-guard` | Patch risk checks |
| `agent-contract-diff` | Contract change analysis |
| `agent-rules-resolve` | Applicable instruction and precedence resolution |
| `agent-release-guard` | Release integrity and provenance checks |
| `agent-context-pack` | Assembly of downstream context |

These are responsibility boundaries, not claims that integrations already exist. Consumers must preserve evidence, unknowns, and partial coverage, and must make their own execution and authorization decisions.

## Explicitly out of scope

V1 excludes AST analysis; dependency, import, and caller graphs; change impact and affected-test selection; test/build execution; package installation; project fixes; compiler-error parsing; Git diff inspection; patch-risk and breaking-contract detection; instruction precedence resolution; LLM inference; repository summarization; automatic context packing; interactive UI; full Python/Go/Rust/Java support; and deep CI parsing.

## Implementation and verification

The runtime has no package dependencies. Development uses TypeScript, Node's built-in test runner, and AJV only for schema tests:

```sh
npm ci --ignore-scripts
npm run typecheck
npm run build
npm test
```

The `.github/workflows/ci.yml` matrix is prepared for Windows, macOS, and Linux on Node 18.18, 20, and 22. A workflow definition is not itself a completed platform run; unavailable platform results remain unverified in the final delivery report.

The implementation is intentionally CLI/JSON-first and does not expose an additional public JavaScript API requirement.

## Documentation

- [Specification](SPEC.md): external behavior and V1 contracts.
- [Architecture](DESIGN.md): responsibilities, data flow, and trust boundaries.
- [Decisions](DECISIONS.md): implementation decisions, evidence boundaries, and remaining lifecycle decisions.
- [Verification plan](TEST_PLAN.md): frozen fixtures, safety checks, and release acceptance.
- [Delivery roadmap](ROADMAP.md): implementation, verification, and release status.
- [Implementation brief](IMPLEMENTATION_BRIEF.md): implementation scope, safety boundary, and handoff evidence.
