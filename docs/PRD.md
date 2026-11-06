# PRD.md:  "The Why"; Product requirements, benchmark metadata table, high-level requirements, etc.

Product Requirements Document

"The Why"; Product requirements, benchmark metadata table, high-level requirements, etc. Defines the benchmark goal, validation objective, test name, benchmark number, category, and high-level success criteria.

## Benchmark Matrix Document Metadata (via benchmark_specification.json)

This PRD.md section is populated from benchmark_specification.json, which is the structured source of benchmark-specific product requirements.

## Workload Number
312

## Workload Name
lmbench Microbench Suite

## Execution Summary (Run and Measure)
Run lmbench lat_syscall (null, read, write), lat_ctx, lat_mem_rd 16 128, bw_mem 512m rd, and bw_pipe once per yaml repetitions, then parse the first float from each stdout, to measure syscall, context-switch, and memory-read latency plus memory and pipe bandwidth. lat_pipe, lat_unix, and lat_proc are not executed

## Main Goal
Measure a fixed lmbench OS microbenchmark subset

## Validation Objective
Validates one parseable float per executed subtest. lat_pipe, lat_unix, and lat_proc are not run

## Workload Category
System Profiling & Performance Analysis

## Validation Requirement

The benchmark must include an automated SQLite-integrated validation layer that verifies persisted results from `results/benchmark.db`. Validation must confirm:

1. The benchmark run completed successfully with no tool errors.
2. Required samples and aggregate metrics were persisted for every swept shape.
3. Metrics are finite and physically sensible (positive, within plausible bounds).
4. Measured values satisfy configured thresholds when the workload defines pass/fail gates.
5. The benchmark fails validation when required data is missing, invalid, or outside bounds.

## Non-Functional Requirements

| Requirement | Target |
|---|---|
| Automation | Runs to completion without manual intervention after `bash run_benchmark.sh` |
| Idempotency | Re-running `run_benchmark.sh` appends a new run; never corrupts existing rows |
| Persistence | All metrics survive script exit; `results/benchmark.db` is the durable record |
