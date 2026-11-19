# SharpeShooter Homework 9 evidence

This repository is the bounded public evidence package for SharpeShooter's
Homework 9 agent-usability test. It contains the standalone agent interface,
the registered synthetic task fixture, and four sanitized before/after evidence
artifacts. It does not contain raw runtime evidence, credentials, authentication
state, databases, private paths, or unrestricted model output.

Repository: <https://github.com/sidsanghvi/sharpeshooter-hw9-evidence>

## Agent interface

SharpeShooter exposes one bounded `run_research` MCP tool backed by the packaged
`sharpeshooter-task` command. Trusted operator policy owns approval, the one-use
out-of-sample allowance, verdict, identities, and runtime paths. The calling agent
receives the documentation, one structured task, and the one authorized tool.

- [Agent-facing interface](docs/agent-interface.md) - [live source](https://github.com/sidsanghvi/sharpeshooter-hw9-evidence/blob/main/docs/agent-interface.md)
- [Registered synthetic task](fixtures/research-task-v1.json) - [live source](https://github.com/sidsanghvi/sharpeshooter-hw9-evidence/blob/main/fixtures/research-task-v1.json)

## Before, one improvement, and after

The preserved baseline was an independent `fail` / `interface`: Codex rejected
the client-visible output schema before any tool call because six enum/const-only
schema nodes lacked explicit types. The baseline was not retried or replaced.

The one observed issue received one type-only improvement. The task, goal,
allowed values, and workflow semantics did not change. A like-for-like
fresh-client rerun produced an independent `pass`, correlated the actual returned
result, and used it for the downstream disposition
`ready_for_human_consideration`.

- [Baseline evidence](evidence/baseline.json) - [live source](https://github.com/sidsanghvi/sharpeshooter-hw9-evidence/blob/main/evidence/baseline.json)
- [Improvement evidence](evidence/improvement.json) - [live source](https://github.com/sidsanghvi/sharpeshooter-hw9-evidence/blob/main/evidence/improvement.json)
- [Before/after comparison](evidence/comparison.md) - [live source](https://github.com/sidsanghvi/sharpeshooter-hw9-evidence/blob/main/evidence/comparison.md)
- [Evidence manifest](evidence/manifest.json) - [live source](https://github.com/sidsanghvi/sharpeshooter-hw9-evidence/blob/main/evidence/manifest.json)

## Verify the public package

From the root of this public repository, verify every published byte listed in
the checksum file:

```sh
shasum -a 256 -c SHA256SUMS
```

The sanitized manifest records the base regeneration command below for the
reviewed SharpeShooter source checkout:

```sh
python scripts/generate_hw9_evidence.py --baseline-root runtime/hw9/baseline --after-root runtime/hw9/improved --source-root . --destination tasks/hw9/evidence
```

Exact deterministic regeneration additionally requires the frozen assistance
disclosure and the immutable ignored generations in the reviewed source
checkout. The registered `baseline` and `improved` generations must never be
rerun or overwritten merely to reproduce these sanitized files. This public
package supports byte verification; it is not a substitute for private raw
evidence custody.

## Frozen claim limits

- The task is bounded and structured; it is not unrestricted prose research.
- Synthetic metrics demonstrate workflow controls, not strategy or investment performance.
- The gateway demonstrates one authorized packaged-command invocation; it is not a hostile-code sandbox or production service.
- A receipt hash is tamper-evidence, not independently signed truth.
- One fresh Codex client does not establish universal agent compatibility.
- Nanda Town is prior integration evidence, not the Homework 9 client.
- Repository evidence readiness is not slides, video, PDF, shared-deck, Canvas, or assignment submission.

Homework 9 is not claimed submitted by this repository.
