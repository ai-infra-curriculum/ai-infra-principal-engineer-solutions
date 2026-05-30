# Self-heal: address verify findings for fill-project-01-distributed-training-solution

## Goal

The previous attempt at this work item produced content that
failed contract verification. Fix the specific findings listed
below by editing only the affected files. Do NOT regenerate
from scratch and do NOT broaden the scope.

## Findings

### 1. `missing_required_sections` (error)

- Target: `?`
- Message: projects/project-01-distributed-training/SOLUTION.md is missing required heading(s): implementation.
- missing: ['implementation']
- seen: ['1. solution overview', '1.1 what the project is actually grading', '1.2 what a passing solution looks like in one paragraph', '1.3 how this document is organised', '2. worked answer — the six load-bearing decisions', '2.1 adr 0001 — primary sharding strategy: fsdp with zero-3 as documented fallback', '2.2 adr 0002 — checkpoint format: sharded + manifest-gated, async uploader, fqn-keyed', '2.3 adr 0003 — cluster manager abstraction: thin per-backend agent behind a single crd-shaped contract', '2.4 adr 0004 — comms backend: nccl on nvidia, gloo as cpu fallback', '2.5 adr 0005 — observability schema: prometheus exposition is the public api, grafana json is the contract', '2.6 adr 0006 — security boundary: identity at the api, scope at the job, secrets at the orchestrator', '3. validation steps — how a reviewer mechanically checks the submission', '3.1 repo discoverability (≤ 5 minutes)', '3.2 demo reproducibility (≤ 30 minutes)', '3.3 reference workload + mfu (≤ 60 minutes including setup)', '3.4 fault tolerance (≤ 60 minutes)', '3.5 multi-cluster (≤ 30 minutes)', '3.6 design artifacts (≤ 60 minutes of reading)', '3.7 code-quality bar', '3.8 communication', '3.9 two qualitative checks the rubric prizes', '4. rubric / review checklist', '4.1 dimension 1 — technical depth & correctness (25 pts)', '4.2 dimension 2 — fault tolerance & operations (20 pts)', '4.3 dimension 3 — design doc & adrs (20 pts)', '4.4 dimension 4 — cross-team adoption / migration (15 pts)', '4.5 dimension 5 — cost / business framing (10 pts)', '4.6 dimension 6 — communication (tech talk + writing) (10 pts)', '4.7 scoring sheet', '5. common mistakes', '6. references', '6.1 pytorch fsdp (official)', '6.10 megatron-lm (nvidia, reference architecture)', '6.11 slurm (official)', '6.12 local exercise context', '6.13 track cross-references', '6.2 deepspeed zero (official)', '6.3 pytorch distributed checkpoint (dcp) (official)', '6.4 kubeflow training operator and `pytorchjob` crd (official)', '6.5 pytorch distributed (official)', '6.6 nccl (nvidia, official)', '6.7 nvidia dcgm (official)', '6.8 cloud workload identity (official)', '6.9 kubernetes security and operator patterns (official)', 'solution — project 01: company-wide distributed training framework']

### 2. `missing_required_sections` (error)

- Target: `projects/project-01-distributed-training/SOLUTION.md`
- Message: projects/project-01-distributed-training/SOLUTION.md is missing required heading(s): implementation.
- missing: ['implementation']
- seen: ['1. solution overview', '1.1 what the project is actually grading', '1.2 what a passing solution looks like in one paragraph', '1.3 how this document is organised', '2. worked answer — the six load-bearing decisions', '2.1 adr 0001 — primary sharding strategy: fsdp with zero-3 as documented fallback', '2.2 adr 0002 — checkpoint format: sharded + manifest-gated, async uploader, fqn-keyed', '2.3 adr 0003 — cluster manager abstraction: thin per-backend agent behind a single crd-shaped contract', '2.4 adr 0004 — comms backend: nccl on nvidia, gloo as cpu fallback', '2.5 adr 0005 — observability schema: prometheus exposition is the public api, grafana json is the contract', '2.6 adr 0006 — security boundary: identity at the api, scope at the job, secrets at the orchestrator', '3. validation steps — how a reviewer mechanically checks the submission', '3.1 repo discoverability (≤ 5 minutes)', '3.2 demo reproducibility (≤ 30 minutes)', '3.3 reference workload + mfu (≤ 60 minutes including setup)', '3.4 fault tolerance (≤ 60 minutes)', '3.5 multi-cluster (≤ 30 minutes)', '3.6 design artifacts (≤ 60 minutes of reading)', '3.7 code-quality bar', '3.8 communication', '3.9 two qualitative checks the rubric prizes', '4. rubric / review checklist', '4.1 dimension 1 — technical depth & correctness (25 pts)', '4.2 dimension 2 — fault tolerance & operations (20 pts)', '4.3 dimension 3 — design doc & adrs (20 pts)', '4.4 dimension 4 — cross-team adoption / migration (15 pts)', '4.5 dimension 5 — cost / business framing (10 pts)', '4.6 dimension 6 — communication (tech talk + writing) (10 pts)', '4.7 scoring sheet', '5. common mistakes', '6. references', '6.1 pytorch fsdp (official)', '6.10 megatron-lm (nvidia, reference architecture)', '6.11 slurm (official)', '6.12 local exercise context', '6.13 track cross-references', '6.2 deepspeed zero (official)', '6.3 pytorch distributed checkpoint (dcp) (official)', '6.4 kubeflow training operator and `pytorchjob` crd (official)', '6.5 pytorch distributed (official)', '6.6 nccl (nvidia, official)', '6.7 nvidia dcgm (official)', '6.8 cloud workload identity (official)', '6.9 kubernetes security and operator patterns (official)', 'solution — project 01: company-wide distributed training framework']

## Output contract

- Edit ONLY the files listed in the findings.
- Preserve the existing content; add or rename headings
  rather than rewriting whole sections.
- Do NOT touch CURRICULUM.md, VERSIONS.md, or anything
  outside the affected files.
