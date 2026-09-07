---
name: github-project
description: >
  Conventions for preparing and maintaining my personal projects on GitHub.
disable-model-invocation: true
---

# General

Treat the repository as a public GitHub project.

Keep repository-facing infrastructure simple and proportional to the project's size.

# Versioning

For versioned projects, derive build identity from Git instead of maintaining versions manually in source files.

- A release tag is exactly the release version, e.g. `1.4.2`; do not use a `v` prefix unless required by external tooling.
- For tagged builds, use the exact tag as the version.
- When a numeric build/version code is required, use the repository commit count at that revision.
- For untagged development builds, derive a non-release version as `<YYYYMMDD>-<short-commit-hash>`, e.g. `20260830-aabbccdd`, using the same versioning mechanism.
- Resolve version information once at build time and use the same resolved identity for all modules and produced artifacts.
- Do not invoke Git or derive version information at application runtime.

# GitHub Actions

- Add useful CI for building and verifying the project.
- Use the latest stable major versions of established actions.
- Run the same build/test commands used locally.
- Prefer project-provided wrappers and scripts over duplicating build logic in workflow YAML.
- Use official caching mechanisms where useful.
- Keep workflow permissions minimal.

## CI

### Build check

- Run on changes to the default branch and on pull requests targeting it.
- Do not run build/test jobs for documentation-only changes. Documentation-specific jobs may run only when relevant documentation changes.
- Build the project and run tests when present.
- Upload useful build outputs as GitHub Actions artifacts with a short retention period.

### Tag build

- Trigger releases from semantic version tags.
- Prefer `*.*.*` tags over `v*.*.*` unless tooling requires the `v` prefix.
- Only release commits reachable from the protected release/default branch.
- Create a GitHub Release for the tag.
- Attach release build outputs to the GitHub Release when applicable.
- For projects that produce Docker images, publish them to GitHub Container Registry (GHCR) by default. Use another registry only when explicitly requested.
- Tag container images with the release version and update `latest` for normal stable releases.
- Do not invent polished release notes from commit history. Prefer a concise commit list, grouped when explicit commit metadata such as `Changelog: frontend` is available.

## Dependabot

Always configure Dependabot.

- Cover every supported dependency ecosystem used in the monorepo.
- Cover dependency manifests in all relevant modules/subprojects, not only the repository root.
- Always include the `github-actions` ecosystem to keep CI actions updated.
- Prefer grouped updates where appropriate to avoid excessive dependency PR noise.

# Markdown

- Do not hard-wrap prose. Keep each paragraph on a single source line and let the renderer handle visual wrapping.
- Separate paragraphs with a blank line. Use explicit line breaks only when semantically required.
- Keep formatting simple and conventional. Do not add structure or formatting that does not improve readability.
- Use direct, factual language. Avoid marketing language, exaggerated claims, and promotional phrasing.

## README

Maintain a README.md at the repository root.

- Keep it concise and practical.
- Write for project users first, not as a dump of internal development knowledge.
- Explain what the project is and how to use it.
- Include build/development instructions only when they are genuinely useful to someone working on the project.
- Do not document internal implementation, CI, release, versioning, repository conventions, or other maintenance mechanics unless users or contributors need to interact with them directly.
- Prefer commands over explanations when the command is self-explanatory.
- Do not explain behavior that is obvious from standard project files such as `Makefile`, `Cargo.toml`, `package.json`, or Gradle configuration.
- Do not add generic sections merely because GitHub READMEs commonly have them.
- Do not add badges unless they provide useful information.

# Licensing

- Use WTFNMFPL unless I explicitly request another license.
- Keep the original license text in `LICENSE`.
- Use `WTFNMFPL` as the SPDX license identifier where applicable.
- Keep copyright information in `COPYRIGHT`.

`COPYRIGHT` should normally look like:

```text
Copyright (C) 2026 Ilya Dobryakov <https://github.com/fopwoc>

Licensed under WTFNMFPL.
```

## Third-party notices

Create `THIRD_PARTY.md` when the project contains third-party material, adapted work, copied or derived assets/code, reverse-engineered behavior, inherited project files, or anything else with licensing/provenance that deserves explicit explanation.

- Describe what was used, where it came from, how it relates to the project, and any relevant licensing or redistribution constraints.
- Clearly identify parts of the project that are not covered by the project's main license.
- Preserve required upstream notices and link to original sources/licenses when useful.
- Do not use `THIRD_PARTY.md` as a dependency/license inventory. Ordinary package dependencies do not need notices unless their license or attribution requirements make them relevant.
- When `THIRD_PARTY.md` exists, reference it from `COPYRIGHT`.

# AI usage

For AI-directed projects, create a concise `AI_USAGE.md`.

Use this structure:

- `# AI usage`
- `## Involvement` — briefly describe the overall role of AI in development and what remains under human direction.
- `## Models` — list the models materially used.
- End with a short note that this describes development provenance and does not imply AI authorship or copyright ownership.

Describe the overall development process, not individual tasks or commits.

Keep the disclosure informal, factual, and concise.

Add a short notice near the end of `README.md` linking to `AI_USAGE.md`:

```markdown
> [!NOTE]
> This project contains AI-generated code. See [AI_USAGE.md](AI_USAGE.md) for details.
```
