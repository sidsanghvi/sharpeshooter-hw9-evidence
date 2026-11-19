# SharpeShooter agent interface

SharpeShooter accepts one bounded, structured research task, runs its prespecified candidate strategies through a deterministic synthetic workflow, obtains an independent review, and returns a correlated receipt. It helps an agent prepare a controlled research handoff; it does not identify a profitable strategy.

## Access and setup

In the disposable client environment, use the MCP server named `sharpeshooter_usability`. It exposes exactly one tool, `run_research`. No shell, repository checkout, dataset, database, or other SharpeShooter tool is available. The test operator authorizes one exact task digest before the client starts; the bearer and runtime paths are supplied by the harness and are not model-visible inputs.

## Prerequisites and operator setup

The operator needs Docker with Compose, Codex CLI 0.155.1 authenticated through the user's ChatGPT subscription, and the reviewed local SharpeShooter source/wheel. From the reviewed repository root, verify `docker info`, then build only the two HW9 services without mutable provenance attestations:

```sh
docker compose --project-directory "$PWD" --project-name sharpeshooter --env-file /dev/null -f "$PWD/compose.yaml" --profile hw9 build --provenance=false hw9-client hw9-gateway
```

Validate the registered task with `sharpeshooter-task validate --task <task-file>` and compare that digest with the separately approved digest. Do not put provider API keys, the bearer, or operator-owned approval/OOS/verdict values in client-visible files.

Create a new absent generation name under ignored `runtime/hw9/`; names are never reused. The runner stages only this document, the task JSON, the client goal, and the final-output schema. It checks the exact in-container login text before starting the internal gateway.

## Launch and access

From the reviewed repository root, the operator launches one bounded generation with `python scripts/run_hw9_client.py <generation>`. The runner starts the no-host-port gateway, mounts only the staged read-only workspace and Codex authentication into the client, configures the single MCP server/tool, and records bounded raw artifacts below that generation. The agent chooses how to use the documented tool; these operator steps do not prescribe a tool-call script or expected disposition.

## Reproduce and clean up

To reproduce setup without consuming an authorized generation, run the focused deterministic suite from the reviewed repository root:

```sh
env PYTHONPATH=.:src PYTHONDONTWRITEBYTECODE=1 ../../.venv/bin/python -m pytest -p no:cacheprovider -q --tb=short tests/test_hw9_client_app.py tests/test_hw9_client_runner.py tests/test_hw9_evidence.py tests/test_research_task.py tests/test_task_cli.py tests/test_task_runner.py tests/test_receipts.py tests/test_misuse.py
```

Inspect the effective services with `docker compose --profile hw9 config`. A registered generation is create-only and is never silently rerun. On every success or failure the runner stops/removes the gateway and verifies both gateway and named client containers are absent. Confirm with `docker compose --profile hw9 ps --quiet hw9-gateway`; preserve the ignored generation directory for evidence, and do not manually delete or overwrite it.

## Input

Call `run_research` with an object containing one `task` property. The task must conform to `sharpeshooter.research-task.v1`: a `client_task_id`, a sourced idea, two to eight distinct complete candidate strategy specifications, and one `selected_candidate_id`. Text and parameters are bounded. Approval digest, OOS confirmation, verdict, identities, paths, datasets, credentials, and evidence policy are operator-owned and are rejected if placed in task content.

## Output and result use

On success the tool returns `sharpeshooter.research-result.v1`. Correlate `task_digest` and `client_task_id`; identify the selected candidate and `selected_is_strategy_hash`; verify author/reviewer separation, `review_id`, `receipt_id`, `receipt_integrity_hash`, and `receipt_verification`; and account for `budget_before`, `budget_after`, and `receipt_consumed_by_scout`. Use those actual fields to choose a downstream disposition. Each reason is a structured `{"field": ..., "value": ...}` fact and must reproduce the returned value (using `independent_review` for author/reviewer separation and `receipt_consumed` for `receipt_consumed_by_scout`). `ready_for_human_consideration` must cite verified receipt and independent-review facts; `rejected` may cite the synthetic-metrics limitation; `incomplete` requires a returned negative verification, review-separation, or consumption fact. A successful tool call alone is not a useful handoff, and no particular disposition is required when more than one is consistent with the returned facts.

## Complete example (not an authorized test task)

```json
{
  "task": {
    "schema_version": "sharpeshooter.research-task.v1",
    "client_task_id": "example-quality-carry-1",
    "idea": {
      "title": "Synthetic quality carry comparison",
      "hypothesis": "A prespecified quality screen may change a bounded synthetic carry ranking.",
      "rationale": "Compare two fixed variants for workflow demonstration only.",
      "sources": [{"uri": "https://example.org/research/quality-carry", "label": "Example research note", "accessed_on": "2026-11-17"}]
    },
    "candidates": [
      {"candidate_id": "carry-quality-a", "strategy_spec": {"name": "Carry quality A", "universe": "eligible synthetic instruments", "signal": "fixed carry rank with quality screen A", "entry_rule": "enter top quintile", "exit_rule": "exit below top two quintiles", "position_sizing": "equal weight, long only", "rebalance": "monthly", "parameters": {"quality_floor": 0.6, "max_position_weight": 0.1}}},
      {"candidate_id": "carry-quality-b", "strategy_spec": {"name": "Carry quality B", "universe": "eligible synthetic instruments", "signal": "fixed carry rank with quality screen B", "entry_rule": "enter top decile", "exit_rule": "exit below top quintile", "position_sizing": "equal weight, long only", "rebalance": "monthly", "parameters": {"quality_floor": 0.7, "max_position_weight": 0.08}}}
    ],
    "selected_candidate_id": "carry-quality-b"
  }
}
```

This example explains the contract but is not authorized by the registered gateway.

## Permissions, limits, and failures

The gateway permits one invocation for the exact preauthorized digest. A changed task, second call, timeout, invalid result, or command failure returns one bounded generic error and does not expose task bytes, paths, credentials, stdout, or stderr. A claimed call remains consumed even when its child fails; request a new operator-created generation rather than retrying. The interface is synchronous and intentionally does not support free-form research, arbitrary code, dynamic datasets, approval changes, or resumability.

If access fails before a tool result, report the observed failure without guessing. If a result is returned, validate its correlations and limits before using it. Never interpret a receipt hash as an independent signature.

## Claim limits

- The task is bounded and structured, not unrestricted prose research.
- Synthetic metrics demonstrate workflow controls, not strategy or investment performance.
- The gateway demonstrates one authorized packaged-command invocation; it is not a hostile-code sandbox or production service.
- A receipt hash is tamper-evidence, not independently signed truth.
- One fresh Codex client does not establish universal agent compatibility.
- Nanda Town is prior integration evidence, not this client.
