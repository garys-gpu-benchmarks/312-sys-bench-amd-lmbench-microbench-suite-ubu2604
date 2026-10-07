# lmbench Microbench Suite Benchmark

[![License](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![CI](https://img.shields.io/badge/CI-host--safe-green.svg)](.github/workflows/ci.yml)

Target: Ubuntu 26.04 · AMD · see Hardware Requirements. This is a host benchmark, not a laptop `pip install` project.

## Quick Start

```bash
git clone https://github.com/garys-gpu-benchmarks/312-sys-bench-amd-lmbench-microbench-suite-ubu2604.git
cd 312-sys-bench-amd-lmbench-microbench-suite-ubu2604
sudo bash setup.sh --assume-yes
bash run_benchmark.sh --profile smoke --validate
```
Results are written to `results/benchmark.db` and `results/summary.json`.

This workload is executed on the validation host after the repository is copied there. `setup.sh` and `run_benchmark.sh` do not open an outbound SSH session.

Prerequisites: Ubuntu 26.04; AMD; Python 3.14.4; root or sudo for `setup.sh`. Framework: Bash, SQLite, Python, PyYAML, C (not C++), lmbench. This is a host benchmark, not a laptop `pip install` project.

```mermaid
flowchart LR
  setup.sh --> run_benchmark.sh --> parse_results.py --> results/benchmark.db
```

## 1. Overview

Runs a fixed lmbench subset: lat_syscall null, read, and write; lat_ctx; lat_mem_rd 16 128; bw_mem 512m rd; bw_pipe. repetitions: how many times the subset is repeated. parallel_jobs, warmup_duration_us, and per-test enable flags are yaml fields and are ignored. Does not run lat_pipe, lat_unix, or lat_proc. Sweep dimensions: parallel_jobs, warmup_duration_us, lat_ctx, lat_mem_rd, bw_mem, bw_pipe, lat_syscall, repetitions.

## 2. What It Validates

- Validates one parseable float per executed subtest. lat_pipe, lat_unix, and lat_proc are not run
- #1: Null syscall latency, us (lat_syscall_null_us); is present and physically sensible.
- #2: Context-switch latency, us (context_switch_latency_us); is present and physically sensible.
- #3: 16 MB stride-128 read latency, ns (lat_mem_rd_16mb_stride128_ns); is present and physically sensible.
- #4: Memory read bandwidth, MB/s (bw_mem_mb_s); is present and physically sensible.
- #5: Inter-process pipe bandwidth, MB/s (bw_pipe_mb_s) is present and physically sensible.

## 3. Metrics Captured

- **#1: Null syscall latency, us** — stored as `lat_syscall_null_us`.
- **#2: Context-switch latency, us** — stored as `context_switch_latency_us`.
- **#3: 16 MB stride-128 read latency, ns** — stored as `lat_mem_rd_16mb_stride128_ns`.
- **#4: Memory read bandwidth, MB/s** — stored as `bw_mem_mb_s`.
- **#5: Inter-process pipe bandwidth, MB/s** — stored as `bw_pipe_mb_s`.

## 4. Hardware Requirements

### Supported environment

- OS: Ubuntu 26.04
- GPU vendor: AMD
- Framework family: Bash, SQLite, Python, PyYAML, C (not C++), lmbench
- Python: Python 3.14.4

### Reference validation environment

The tables below describe the machine used to generate the reference results. They are not a requirement that every user buy that exact cloud instance.

### System

Ubuntu 26.04 / AMD / Bash, SQLite, Python, PyYAML, C (not C++), lmbench

### GPU

Ubuntu 26.04 / AMD / Bash, SQLite, Python, PyYAML, C (not C++), lmbench

## 5. Software Requirements

| Component | Version |
|---|---|
| OS | Ubuntu 26.04 |
| Kernel | kernel 7.0.0 |
| Python | Python 3.14.4 |
| ROCm | N/A - ROCm not used |
| rocBLAS | N/A - rocBLAS not used |

Runs a fixed lmbench subset: lat_syscall null, read, and write; lat_ctx; lat_mem_rd 16 128; bw_mem 512m rd; bw_pipe. repetitions: how many times the subset is repeated. parallel_jobs, warmup_duration_us, and per-test enable flags are yaml fields and are ignored.

## 6. Installation

```bash
Run lmbench lat_syscall (null, read, write), lat_ctx, lat_mem_rd 16 128, bw_mem 512m rd, and bw_pipe
```

## 7. Running the Benchmark

```bash
Run lmbench lat_syscall (null, read, write), lat_ctx, lat_mem_rd 16 128, bw_mem 512m rd, and bw_pipe
```

**Validating results separately:**

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional
python3 -m venv .venv
source ".venv/bin/activate"
".venv/bin/python" scripts/validate_results.py
```

## 8. Output

### `results/benchmark.db` (SQLite)

CSV with benchmark, units, and value columns

benchmark,units,value
lat_syscall_null_us,us,0.12
lat_mem_rd_16mb_stride128_ns,ns,45
bw_mem_mb_s,MB/s,24000

```bash
Run lmbench lat_syscall (null, read, write), lat_ctx, lat_mem_rd 16 128, bw_mem 512m rd, and bw_pipe
```

### `results/summary.json`

Consolidated metrics from the most recent run — suitable for CI artifact upload or dashboard ingestion.

### `results/raw/<timestamp>.txt`

CSV with benchmark, units, and value columns

benchmark,units,value
lat_syscall_null_us,us,0.12
lat_mem_rd_16mb_stride128_ns,ns,45
bw_mem_mb_s,MB/s,24000

## 9. Baselines / Thresholds

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

## 10. Troubleshooting

**`setup.sh` missing collector**
Create cannot finish without `scripts/collect_workload.py`.

**`self_check` overlay rewritten**
Do not overwrite files listed in `results/overlay_lock.json`.

**Remote SSH drop during setup**
Reconnect and resume `bash setup.sh --assume-yes`. Do not wipe `.venv` or `.cache`.

## 11. NVIDIA H100 Coding Differences

Primary target is AMD ROCm. NVIDIA notes in this section are reference only and are not the execution path.

## Repository layout

```text
.
├── setup.sh
├── run_benchmark.sh
├── benchmark_specification.json
├── config/
├── scripts/
├── src/
├── tests/
├── docs/
├── results/
└── LICENSE
```
