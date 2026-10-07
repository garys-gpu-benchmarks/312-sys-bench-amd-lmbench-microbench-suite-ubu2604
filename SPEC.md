# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Runs a fixed lmbench subset: lat_syscall null, read, and write; lat_ctx; lat_mem_rd 16 128; bw_mem 512m rd; bw_pipe. repetitions: how many times the subset is repeated. parallel_jobs, warmup_duration_us, and per-test enable flags are yaml fields and are ignored. Does not run lat_pipe, lat_unix, or lat_proc. Sweep dimensions: parallel_jobs, warmup_duration_us, lat_ctx, lat_mem_rd, bw_mem, bw_pipe, lat_syscall, repetitions.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| parallel_jobs | `--parallel-jobs` | smoke=1, baseline=2, extended=2 | 2 | From Parameter list; see Execution Description With Parameters. |
| warmup_duration_us | `--warmup-duration-us` | smoke=1000, baseline=1000000, extended=1000000 | 1000000 | From Parameter list; see Execution Description With Parameters. |
| lat_ctx | `--lat-ctx` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| lat_mem_rd | `--lat-mem-rd` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| bw_mem | `--bw-mem` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| bw_pipe | `--bw-pipe` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| lat_syscall | `--lat-syscall` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| repetitions | `--repetitions` | smoke=1, baseline=5, extended=14 | 5 | From Parameter list; see Execution Description With Parameters. |
| output_format | `--output-format` | smoke=csv, baseline=csv, extended=csv | csv | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Run lmbench lat_syscall (null, read, write), lat_ctx, lat_mem_rd 16 128, bw_mem 512m rd, and bw_pipe
```

## Raw Output Format

CSV with benchmark, units, and value columns

benchmark,units,value
lat_syscall_null_us,us,0.12
lat_mem_rd_16mb_stride128_ns,ns,45
bw_mem_mb_s,MB/s,24000

## Metrics

## Framework

Runs a fixed lmbench subset: lat_syscall null, read, and write; lat_ctx; lat_mem_rd 16 128; bw_mem 512m rd; bw_pipe. repetitions: how many times the subset is repeated. parallel_jobs, warmup_duration_us, and per-test enable flags are yaml fields and are ignored.

## Installation and Execution Summary

Run lmbench lat_syscall (null, read, write), lat_ctx, lat_mem_rd 16 128, bw_mem 512m rd, and bw_pipe once per yaml repetitions, then parse the first float from each stdout, to measure syscall, context-switch, and memory-read latency plus memory and pipe bandwidth. lat_pipe, lat_unix, and lat_proc are not executed

## Platform Portability

- **AMD (primary):** ```bash
Run lmbench lat_syscall (null, read, write), lat_ctx, lat_mem_rd 16 128, bw_mem 512m rd, and bw_pipe
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
5. All required aggregate metrics are physically sensible (positive values). Runs a fixed lmbench subset: lat_syscall null, read, and write; lat_ctx; lat_mem_rd 16 128; bw_mem 512m rd; bw_pipe. repetitions: how many times the subset is repeated. parallel_jobs, warmup_duration_us, and per-test enable flags are yaml fields and are ignored.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Runs a fixed lmbench subset: lat_syscall null, read, and write; lat_ctx; lat_mem_rd 16 128; bw_mem 512m rd; bw_pipe. repetitions: how many times the subset is repeated. parallel_jobs, warmup_duration_us, and per-test enable flags are yaml fields and are ignored.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.
