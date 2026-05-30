# Address CI failures on PR #7

## Goal

The PR you just opened failed CI. Fix the failures listed
below by editing files on the current branch. Do NOT regenerate
the content from scratch — make the minimal edit needed to
satisfy each failing check.

## Failed checks

### 1. `Markdown lint` (failure)

- Details: <https://github.com/ai-infra-curriculum/ai-infra-principal-engineer-solutions/actions/runs/26696133150/job/78680872983>
- Annotations:
  - `.github:2` (warning): Node.js 20 actions are deprecated. The following actions are running on Node.js 20 and may not work as expected: actions/checkout@v4, DavidAnson/markdownlint-cli2-action@v16. Actions will be forced to run with Node.js 24 by default starting June 16th, 2026. Node.js 20 will be removed from the runner
  - `.github:16` (failure): Failed with exit code: 1
  - `projects/project-04-innovation-poc/SOLUTION.md:86` (failure): projects/project-04-innovation-poc/SOLUTION.md:86:1 MD004/ul-style Unordered list style [Expected: dash; Actual: plus] https://github.com/DavidAnson/markdownlint/blob/v0.34.0/doc/md004.md

## Output contract

- Edit ONLY files inside this repo on the current branch.
- Preserve the existing structure; do not delete sections.
- Do NOT touch CURRICULUM.md, README.md, or VERSIONS.md.
- One atomic commit covering all fixes is fine.
