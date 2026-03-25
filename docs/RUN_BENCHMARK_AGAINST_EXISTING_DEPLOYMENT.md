# Running Benchmark Against Existing llm-d Deployment

This guide explains how to run benchmarks against an already-deployed llm-d infrastructure with simulated accelerators.

## Requirements

- ✅ llm-d infrastructure deployed and running in `llmdbench` namespace
- **No Python venv needed** - The script is bash-based and uses kubectl. Python and benchmark harnesses are inside the Docker image, not on your local machine
- Tools required on your machine:
   - `kubectl`
   - `yq` (YAML processor) - install with `brew install yq` if missing
   - `gsed` (GNU sed for macOS) - usually already available


## Manual Benchmark Execution

The `run_only.sh` script might lead to issues with automatic execution. Use this manual approach instead:

1. Create the Benchmark Pod `llmdbench-harness-launcher`

```bash
cd existing_stack
./run_only.sh -c sim_config.yaml -o ~/llm-d-results -v
```

This creates the harness pod and generates the workload profile, but doesn't execute the benchmark automatically.

2. Open an interactive terminal session inside the `llmdbench-harness-launcher` container:

```bash
kubectl exec -it -n llmdbench llmdbench-harness-launcher -- /bin/bash
```

3. Trigger the Benchmark

```bash
python3 -m inference_perf.main \
  -c /workspace/profiles/inference-perf/sanity_random.yaml \
  --log-level INFO
```

The benchmark will run for ~60 seconds and generate results in `/workspace/`.

4. Copy the results to Local Machine


```bash
kubectl cp llmdbench/llmdbench-harness-launcher:/workspace/summary_lifecycle_metrics.json ~/llm-d-results/summary_lifecycle_metrics.json
kubectl cp llmdbench/llmdbench-harness-launcher:/workspace/stage_0_lifecycle_metrics.json ~/llm-d-results/stage_0_lifecycle_metrics.json
kubectl cp llmdbench/llmdbench-harness-launcher:/workspace/per_request_lifecycle_metrics.json ~/llm-d-results/per_request_lifecycle_metrics.json
kubectl cp llmdbench/llmdbench-harness-launcher:/workspace/config.yaml ~/llm-d-results/config.yaml
```


You should see:
- `summary_lifecycle_metrics.json` - Overall performance summary
- `stage_0_lifecycle_metrics.json` - Stage-specific metrics
- `per_request_lifecycle_metrics.json` - Per-request details
- `config.yaml` - Configuration used

## Configuration Files

The benchmark is configured in [`existing_stack/sim_config.yaml`](existing_stack/sim_config.yaml):

```yaml
# Endpoint configuration (existing deployment)
endpoint_stack_name: ms-sim
endpoint_namespace: llmdbench
endpoint_model: random
endpoint_base_url: http://infra-sim-inference-gateway-istio.llmdbench.svc.cluster.local

# Harness configuration
harness_name: inference-perf
harness_namespace: llmdbench
harness_results_pvc: workload-pvc  # Required even when using -o for local output
harness_image: ghcr.io/llm-d/llm-d-benchmark:v0.3.7
harness_cpu: 1
harness_memory: 2Gi

# Workload - random synthetic data
workload:
  sanity_random:
    load:
      type: constant
      stages:
      - rate: 2
        duration: 60
    data:
      type: random
      input_distribution:
        total_count: 200  # Increased from 100 to avoid index error
      output_distribution:
        total_count: 200  # Increased from 100 to avoid index error
```

**Important Notes:**
- `harness_results_pvc` field is required even when using `-o` for local output
- `total_count: 200` is needed to work around an off-by-one bug in inference-perf
- Resources kept at 1 CPU / 2Gi to fit within cluster limits

## Result Metrics

The `summary_lifecycle_metrics.json` contains:

- **Total Requests:** 120 (119 successful, 1 failed)
- **Mean Request Latency:** ~3.85ms
- **Median Request Latency:** ~3.61ms
- **Time per Output Token:** ~78.9μs (mean)
- **Load Pattern:** 2 requests/second for 60 seconds

## Why This Approach?

### No need for the full e2e Script (setup/e2e.sh)
- It deploys the entire infrastructure from scratch
- It downloads models - no need when running with a simulator


### Use the run_only.sh Script
- Uses existing infrastructure
- Skips deployment steps
- Creates pod and profile
- **Issue:** Doesn't execute benchmark automatically
- **Use when:** Infrastructure already deployed (your case)

### Manual Execution (This Guide)
- Most reliable for existing deployments
- Full control over benchmark execution
- Easy to debug and verify results
- **Use when:** run_only.sh doesn't work automatically

## Verification

Check that all infrastructure is running:

```bash
kubectl get pods -n llmdbench
```

Expected output:
```
NAME                                                 READY   STATUS    RESTARTS   AGE
gaie-sim-epp-56d4fd8d8f-tgh5l                        1/1     Running   0          15h
infra-sim-inference-gateway-istio-d98d5876c-6ptqb    1/1     Running   0          15h
llmdbench-harness-launcher                           1/1     Running   0          1h
ms-sim-llm-d-modelservice-decode-5b6975cc5b-6wmsz    2/2     Running   0          15h
ms-sim-llm-d-modelservice-decode-5b6975cc5b-dsfrg    2/2     Running   0          15h
ms-sim-llm-d-modelservice-decode-5b6975cc5b-m9tzv    2/2     Running   0          15h
ms-sim-llm-d-modelservice-prefill-7b499bfcdf-k5lps   1/1     Running   0          15h
```

## Troubleshooting

### Missing `harness_results_pvc` Error
If you see `./run_only.sh: line 464: harness_results_pvc: unbound variable`:
- Add `harness_results_pvc: workload-pvc` to your config file
- This field is required even when using `-o` for local output

### Index Out of Bounds Error
If you see `IndexError: index 100 is out of bounds for axis 0 with size 100`:
- Increase `total_count` from 100 to 200 in both input and output distributions
- This works around an off-by-one bug in inference-perf

### Transfer Encoding Errors
Errors like `TransferEncodingError: 400, message='Not enough data to satisfy transfer length header.'` are expected with simulated endpoints and don't prevent results from being generated.

### If harness pod doesn't exist
The `run_only.sh` script will create it automatically.

### If you need to recreate the harness pod
**Note:** The `run_only.sh` script automatically deletes and recreates the pod, so manual deletion is usually not needed.

To manually delete:
```bash
kubectl delete pod -n llmdbench llmdbench-harness-launcher
```

Then run:
```bash
cd existing_stack
./run_only.sh -c sim_config.yaml -o ~/llm-d-results -v
```

### If benchmark fails to connect to endpoint

**1. Check if the service exists:**
```bash
kubectl get svc -n llmdbench infra-sim-inference-gateway-istio
```

**2. Verify it's accessible (test the endpoint):**
```bash
kubectl exec -n llmdbench llmdbench-harness-launcher -- \
  curl -v http://infra-sim-inference-gateway-istio.llmdbench.svc.cluster.local/v1/models
```

**3. Check the gateway pod is running:**
```bash
kubectl get pods -n llmdbench -l app=infra-sim-inference-gateway-istio
```

The first command only confirms the service resource exists. The second command actually tests connectivity and response, which is a better verification of accessibility.

### Delete stuck download-model job
For simulated accelerators, no model download is needed:
```bash
kubectl delete job -n llmdbench download-model --ignore-not-found=true
```

## Notes

- **Stuck download:** Always delete `download-model` job before running benchmarks.  No model download needed - delete any stuck download-model jobs
- The full `./e2e` deployment script includes model download steps. For simulated accelerators, this is unnecessary since:
  1. The infrastructure is already deployed
  2. Simulated accelerators don't need real models
  3. The benchmark harness pod is already created
- **Image version:** Use `ghcr.io/llm-d/llm-d-benchmark:v0.3.7` (`:latest` doesn't exist)


