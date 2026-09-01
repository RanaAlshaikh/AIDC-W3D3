# Lab W3D3: The Engine Swap

## 1. Predictions vs Measurements

### Predictions (by hand)
* Concurrency 8 speedup over static batch 8: 2.24x
* Static batching scaling (1 -> 8): 3.00x
* vLLM scaling (1 -> 8): 3.88x
* Scaling ratio comparison: larger by ~1.30x


### Measured Results
* Baseline Batch 8 Throughput: 98.5 tokens/s
* vLLM Concurrency 8 Throughput: 220.8 tokens/s
* Measured Speedup at Concurrency 8: 2.24x


## 2. Reflection
Static batching suffered from wasted compute slots due to padding and early request termination under mixed sequence lengths. vLLM implements continuous batching and PagedAttention, dynamically admitting waiting requests and reclaiming resources instantly. This eliminates slot inefficiency and enables sustained scaling as concurrency increases.

## 3. Verification

`GREEN CHECK: PASS`

<img width="470" height="71" alt="Screenshot 2026-09-01 at 2 30 25 PM" src="https://github.com/user-attachments/assets/98aaf2a5-e774-4978-a8bf-f03d1699a826" />

---

# Extra Lab W3D3: Load Shedding Under Overload

## 1. Predictions (by hand)
* **Unbounded Burst (n=50):** p95 latency will be much worse due to queue congestion.
* **Shedded Burst (Cap=8):** Accepted requests keep normal concurrency-8 p95 latency; excess requests are shed instantly.


## 2. Benchmark 

| Mode / Run | Sent (n) | Admitted | Shed | p95 Latency (s) |
|---|---|---|---|---|
| **Naive (Unbounded)** | 50 | 50 | 0 | High |
| **Shedded (Cap=8)** | 50 | 8 | 42 | Low |
| **Sweep (n=8)** | 8 | 8 | 0 | Low |
| **Sweep (n=16)** | 16 | 8 | 8 | Low |
| **Sweep (n=32)** | 32 | 8 | 24 | Low |
| **Sweep (n=50)** | 50 | 8 | 42 | Low |


## 3. Verification

`GREEN CHECK: PASS`

<img width="641" height="64" alt="Screenshot 2026-09-01 at 3 29 04 PM" src="https://github.com/user-attachments/assets/e8aa58ee-d3a8-46ae-b8a4-b8ba9284d675" />

