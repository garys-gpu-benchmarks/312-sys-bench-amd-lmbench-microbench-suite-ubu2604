# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Runs a fixed lmbench subset: lat_syscall null and read; lat_ctx; lat_mem_rd 16 128; bw_mem 512m rd; bw_pipe. repetitions: how many times the subset is repeated. warmup_duration_us: lmbench -W, microseconds each test runs untimed before it is timed (lat_mem_rd warms up once per array size). Every tool runs single-threaded (no -P). Sweep dimensions: warmup_duration_us, repetitions.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| warmup_duration_us | `--warmup-duration-us` | smoke=1000, baseline=10000, extended=10000 | 10000 | From Parameter list; see Execution Description With Parameters. |
| repetitions | `--repetitions` | smoke=1, baseline=5, extended=14 | 5 | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Run lmbench lat_syscall (null, read), lat_ctx, lat_mem_rd 16 128, bw_mem 512m rd, and bw_pipe
```

## Raw Output Format

CSV with benchmark, units, and value columns

benchmark,units,value
lat_syscall_null_us,us,0.12
lat_mem_rd_16mb_stride128_ns,ns,45
bw_mem_mb_s,MB/s,24000

## Metrics

## Framework

Runs a fixed lmbench subset: lat_syscall null and read; lat_ctx; lat_mem_rd 16 128; bw_mem 512m rd; bw_pipe. repetitions: how many times the subset is repeated. warmup_duration_us: lmbench -W, microseconds each test runs untimed before it is timed (lat_mem_rd warms up once per array size).

## Installation and Execution Summary

Run lmbench lat_syscall (null and read), lat_ctx, lat_mem_rd 16 128, bw_mem 512m rd, and bw_pipe once per yaml repetitions, then parse the first float from each stdout, to measure syscall, context-switch, and memory-read latency plus memory and pipe bandwidth

## Platform Portability

- **AMD (primary):** ```bash
Run lmbench lat_syscall (null, read), lat_ctx, lat_mem_rd 16 128, bw_mem 512m rd, and bw_pipe
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

CSV with benchmark, units, and value columns

benchmark,units,value
lat_syscall_null_us,us,0.12
lat_mem_rd_16mb_stride128_ns,ns,45
bw_mem_mb_s,MB/s,24000

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --smoke
bash run_benchmark.sh --baseline
bash run_benchmark.sh --extended
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Runs a fixed lmbench subset: lat_syscall null and read; lat_ctx; lat_mem_rd 16 128; bw_mem 512m rd; bw_pipe. repetitions: how many times the subset is repeated. warmup_duration_us: lmbench -W, microseconds each test runs untimed before it is timed (lat_mem_rd warms up once per array size).
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Runs a fixed lmbench subset: lat_syscall null and read; lat_ctx; lat_mem_rd 16 128; bw_mem 512m rd; bw_pipe. repetitions: how many times the subset is repeated. warmup_duration_us: lmbench -W, microseconds each test runs untimed before it is timed (lat_mem_rd warms up once per array size).

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
