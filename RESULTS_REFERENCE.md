# Results: 323 BERT Inference Sweep

**Main goal:** Measure PyTorch BERT inference performance  
**Tools:** Run inline transformers.BertModel.from_pretrained on synthetic token batches

Reference results from the validation run on the system shown in each section (recorded 2026-11-22). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× AMD Instinct MI300X VF (196288 MB) · INTEL(R) XEON(R) PLATINUM 8568Y+ · Ubuntu 26.04.1 LTS · kernel 7.0.0-43-generic · driver 7.1.9.31600000
- **Run:** 2026-11-22T14:53:39Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 01:14 · benchmark 00:01 · whole run 01:22
- **Source:** run 323_20261122_145339
- **Command:** `bash run_benchmark.sh --smoke --model-name bert-base-uncased --dtype f16_r --sequence-len 32 --batch-size 1 --warmup-iters 2 --num-iterations 5`

| # | Metric | Result |
|---|---|---|
| 1 | Inference throughput (sequences_per_sec) | 281.25948899263864 |
| 2 | Inference latency, mean step time, ms (mean_latency_ms) | 3.5554355999920517 |
| 3 | Token throughput (tokens_per_sec) | 9000.303647764436 |
| 4 | Peak GPU memory (peak_gpu_memory_gb) | 0.32100608 |
<!-- /profile:smoke -->
