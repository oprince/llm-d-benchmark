---
layout: default
title: Home
---

# llm-d-benchmark Documentation

Welcome to the llm-d-benchmark documentation. This comprehensive benchmarking framework is designed for LLM inference deployments.

## Quick Links

### Getting Started
- [Quickstart Guide](quickstart.md)
- [Standup Guide](standup.md)
- [Resource Requirements](resource_requirements.md)

### Running Benchmarks
- [Run Guide](run.md)
- [**Running Benchmarks Against Existing Deployment**](RUN_BENCHMARK_AGAINST_EXISTING_DEPLOYMENT.md) - Complete guide for benchmarking existing llm-d infrastructure
- [Run Against Existing Example](tutorials/run/run_against_existing_example.md)
- [Run Interactively Example](tutorials/run/run_interactively_example.md)

### Configuration & Analysis
- [Benchmark Report](benchmark_report.md)
- [Metrics Collection](metrics_collection.md)
- [Design of Experiments (DOE)](doe.md)
- [Observability](observability.md)

### Advanced Topics
- [Lifecycle Management](lifecycle.md)
- [Workload Variant Autoscaler](workload-variant-autoscaler.md)
- [Upstream Versions](upstream-versions.md)
- [Flexibility](flexibility.md)
- [Reproducibility](reproducibility.md)

### Reference
- [FAQ](faq.md)

## Featured Guide

### Running Benchmarks Against Existing Deployment

A comprehensive step-by-step guide for running benchmarks against already-deployed llm-d infrastructure with simulated accelerators.

**Key Topics:**
- Manual benchmark execution workflow
- Configuration setup and troubleshooting
- Result collection and analysis
- Common issues and solutions

[Read the full guide →](RUN_BENCHMARK_AGAINST_EXISTING_DEPLOYMENT.md)

## About

llm-d-benchmark is a comprehensive benchmarking framework designed to evaluate and optimize LLM inference deployments across various configurations and workloads.

### Key Features
- Multiple benchmark harnesses (inference-perf, guidellm, vllm-benchmark)
- Simulated and real GPU support
- Comprehensive metrics collection
- Flexible workload profiles
- Integration with Kubernetes and OpenShift

## Contributing

See [CONTRIBUTING.md](../CONTRIBUTING.md) for guidelines on how to contribute to this project.

## License

This project is licensed under the Apache License 2.0. See [LICENSE](../LICENSE) for details.
