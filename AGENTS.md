# AGENTS.md — layer-rocm

Standalone candy repo for the `rocm` layer — the AMD ROCm HIP runtime, OpenCL
ICD, and GPU management tooling installed from the Fedora system repos. The candy
lives in `charly.yml` at the repo root: the `env:` block, the `security:` block,
the `distro.fedora:` package section, the `check:` probes, and the embedded
`skill:` entity projected into the marketplace corpus as `/charly-distros:rocm`.

Canonical files:

- `charly.yml` — the `rocm:` candy entity and the `rocm-skill:` skill entity.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-distros:rocm` — the owning skill. The ROCm packages, `ROCM_PATH`, the
  `keep-groups` security model, the auto-detected `HSA_OVERRIDE_GFX_VERSION` /
  `DRINODE` runtime env, and the host requirements. Load before editing or
  troubleshooting the candy.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, `distro:` sections, `env:`, `security:`).
  Load before editing any entity field or plan step.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- `charly box validate` at the repo root checks the manifest parses and
  validates.
- The candy's `plan:` `check:` steps assert the installed packages, the `clinfo`
  and `rocm-smi` binaries, and `ROCM_PATH`. Live GPU enumeration is an
  `agent-check`, assertable only on a host with a real AMD GPU passed through.
- `HSA_OVERRIDE_GFX_VERSION` and `DRINODE` are auto-detected at runtime from host
  state — not baked into the candy.

## Modify this repo

- Edit the `rocm:` candy entity AND the `rocm-skill:` skill entity in
  `charly.yml` together. The skill is the projected usage source, so a package
  or env change not mirrored in the skill leaves the corpus stale.
- The `distro.fedora:` section is the only distro arm; a package change must keep
  the matching `check:` probe honest.
- New behaviour claims belong in the `plan:` as an observable `check:` step, and
  in the skill body.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
