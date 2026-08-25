# eval-pinchbench

**PinchBench: Skill Composition for Coding Agents**

> ⚠️ **Third-party eval.** This is a `register/` pointer in inspect_evals — the task code lives in an external repository of unaudited provenance and will execute on OpenEvalz infrastructure. Onboarding it is a security review, not a packaging task.

This is an Inspect AI wrapper of the original PinchBench implementation. It does not contain
its own dataset or scoring logic. The original implementation can be found at
https://github.com/pinchbench/skill. Users provide a pinned checkout of the original
repository, tested here at commit 819384ae830492365b8363fc26bc2602e73f216d, and the wrapper
reports native PinchBench results through Inspect's scoring interface. PinchBench contains 53
real-world OpenClaw agent tasks across productivity, research, writing, coding, analysis,
email, memory, and skill-discovery categories. The native harness grades tasks automatically,
with an LLM judge, or both; this wrapper runs that harness in Docker and reports the native
aggregate score, or the mean of per-task numeric scores when no aggregate is present, with
Inspect mean/stderr metrics. It requires Docker, an OpenAI-compatible model endpoint, the
pinned Docker base image
node:22-bookworm@sha256:c601a46abb4d2ab80a9dc3da208d50d1122642d53f17a101926ace71e5a9bf1c,
openclaw@2026.6.10, and the exact Python dependencies pinned in the upstream pyproject.toml
and Dockerfile.

## At a glance

| | |
|---|---|
| Upstream | [`register/pinchbench`](https://github.com/UKGovernmentBEIS/inspect_evals/tree/main/register/pinchbench) |
| Group | — |
| Total samples | 53 |
| Execution class | `sandbox-local` |
| Cost class | `high` |
| Flags | sandboxed · needs internet |
| Tags | — |

### Tasks

| Task | Samples |
|---|---|
| `pinchbench` | 53 |

### External assets

_None declared upstream._

## Running one problem

OpenEvalz is problem-level: the atomic unit is a single sample, not the whole eval.

```bash
inspect eval inspect_evals/pinchbench \
  --sample-id "<sample-id>" \
  --model openai-api/trustedrouter/<model> \
  --token-limit 200000
```

> **Two things that bite here, both verified in Inspect's source.**
>
> 1. **`--cost-limit` does not work on this routing path.** Inspect only records cost when its
>    pricing table resolves the model, and `_model_info.py` strips only `azure|bedrock|vertex`
>    prefixes — so `trustedrouter/<model>` never resolves and the cap silently never binds. The
>    real spend cap is enforced **server-side by TrustedRouter** via the delegated key's
>    `limit_microdollars` and spend window. Use `--token-limit` as the in-process bound.
> 2. **`--sample-id` matches with `fnmatch`.** A glob silently selects many samples and only warns.
>    Always pass a literal id.

## Reproducibility

`bundle.template.json` is the contract. A run that cannot emit a complete bundle does not publish.
Every image is pinned by `sha256` digest and every dataset by revision.

## Licensing

OpenEvalz wrapper code in this repository is **Business Source License 1.1** (see `LICENSE`) —
Licensor Lore Hex Corp, Change Date four years from publication, Change License Apache 2.0, no
Additional Use Grant. Same terms as TrustedRouter. Source-available, not open source: you may read,
modify and make non-production use of it, but production use needs a commercial licence
(licensing@openevalz.com).

**The packaged evaluation is NOT relicensed.** The task code, dataset and container images come from
upstream under their own terms — inspect_evals is MIT (UK AI Security Institute), and individual
datasets and images carry their own, sometimes unstated, licences. BSL covers only the OpenEvalz
packaging around them. See `NOTICE.md`, which must be completed before this repo publishes anything.
