# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Runs Hugging Face BertModel.from_pretrained(model_name) on synthetic token ids. sequence_len, batch_size, warmup_iters, num_iterations, model_name, and dtype (fp16 when '16' is in yaml) come from yaml. precision, num_gpus, and prompt_source are ignored. Latency percentiles equal the mean. Sweep dimensions: model_name, dtype, precision, num_gpus, prompt_source, sequence_len, batch_size, warmup_iters.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| model_name | `--model-name` | smoke=bert-base-uncased, baseline=bert-base-uncased, extended=bert-base-uncased | bert-base-uncased | From Parameter list; see Execution Description With Parameters. |
| dtype | `--dtype` | smoke=f16_r, baseline=f16_r, extended=f16_r | f16_r | From Parameter list; see Execution Description With Parameters. |
| precision | `--precision` | smoke=mixed_float16, baseline=mixed_float16, extended=mixed_float16 | mixed_float16 | From Parameter list; see Execution Description With Parameters. |
| num_gpus | `--num-gpus` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| prompt_source | `--prompt-source` | smoke=synthetic, baseline=synthetic, extended=synthetic | synthetic | From Parameter list; see Execution Description With Parameters. |
| sequence_len | `--sequence-len` | smoke=32, baseline=128, extended=256 | 128 | From Parameter list; see Execution Description With Parameters. |
| batch_size | `--batch-size` | smoke=1, baseline=4, extended=8 | 4 | From Parameter list; see Execution Description With Parameters. |
| warmup_iters | `--warmup-iters` | smoke=2, baseline=2, extended=5 | 2 | From Parameter list; see Execution Description With Parameters. |
| num_iterations | `--num-iterations` | smoke=5, baseline=52500, extended=149000 | 52500 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Run inline transformers.BertModel.from_pretrained on synthetic token batches
```

## Raw Output Format

CSV with one configuration row. A single row is duplicated to two samples

sample_index,batch_size,sequence_len,sequences_per_sec,p50_latency_ms,mean_latency_ms,p95_latency_ms,p99_latency_ms,tokens_per_sec,peak_gpu_memory_gb
0,1,32,80,12.5,12.5,12.5,12.5,2560,2.0

## Metrics

- **#1: Inference throughput** — stored as `sequences_per_sec`.
- **#2: Inference latency, mean step time, ms** — stored as `mean_latency_ms`.
- **#3: Token throughput** — stored as `tokens_per_sec`.
- **#4: Peak GPU memory** — stored as `peak_gpu_memory_gb`.

## Framework

Runs Hugging Face BertModel.from_pretrained(model_name) on synthetic token ids. sequence_len, batch_size, warmup_iters, num_iterations, model_name, and dtype (fp16 when '16' is in yaml) come from yaml. precision, num_gpus, and prompt_source are ignored.

## Installation and Execution Summary

Run inline Hugging Face Transformers BertModel on synthetic token batches with yaml sequence_len and batch_size, then write sequences/s, tokens/s, and mean latency as p50/p95/p99, to measure PyTorch BERT inference. This is not TensorFlow

## Platform Portability

- **AMD (primary):** ```bash
Run inline transformers.BertModel.from_pretrained on synthetic token batches
```
- **NVIDIA:** Primary target is AMD ROCm. NVIDIA notes in this section are reference only and are not the execution path.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

CSV with one configuration row. A single row is duplicated to two samples

sample_index,batch_size,sequence_len,sequences_per_sec,p50_latency_ms,mean_latency_ms,p95_latency_ms,p99_latency_ms,tokens_per_sec,peak_gpu_memory_gb
0,1,32,80,12.5,12.5,12.5,12.5,2560,2.0

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Runs Hugging Face BertModel.from_pretrained(model_name) on synthetic token ids. sequence_len, batch_size, warmup_iters, num_iterations, model_name, and dtype (fp16 when '16' is in yaml) come from yaml. precision, num_gpus, and prompt_source are ignored.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Runs Hugging Face BertModel.from_pretrained(model_name) on synthetic token ids. sequence_len, batch_size, warmup_iters, num_iterations, model_name, and dtype (fp16 when '16' is in yaml) come from yaml. precision, num_gpus, and prompt_source are ignored.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
