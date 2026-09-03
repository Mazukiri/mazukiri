<h1 align="center">Mai Hoàng Đức</h1>
<h3 align="center">GPU &amp; ML Systems Engineering — ROCm/HIP kernels, LLM inference, performance measurement</h3>

<p align="center">
  <a href="https://linkedin.com/in/hoangducmai">
    <img src="https://img.shields.io/badge/LinkedIn-hoangducmai-blue?style=for-the-badge&logo=linkedin" alt="LinkedIn" />
  </a>
  <a href="mailto:maihoangduc04@gmail.com">
    <img src="https://img.shields.io/badge/Email-maihoangduc04-red?style=for-the-badge&logo=gmail" alt="Email" />
  </a>
</p>

---

B.Sc. Computer Science at **VNU University of Science, Vietnam National University**, Hanoi —
expected **July 2027**. Currently **GPU Engineer Intern at Moreh**, a Korean AI-infrastructure
company, working on the AMD MI250/MI300X training and serving stack.

I like problems where the answer is a number, and where finding it means reading someone else's
code carefully. Most of what is below started as "this measurement looks wrong".

---

### Upstream

**[ROCm/rocm-libraries#9985](https://github.com/ROCm/rocm-libraries/issues/9985)** — rocBLAS picks
a slower GEMM kernel than one already shipped inside the same library. Measured over **7,009
shapes** on MI250: the default loses on **75%** of them, by **≥10% on half** — median 1.10×, 9.2×
at p99 — and reproduces on MI300X at median 1.30×. Open, with **two AMD engineers assigned**.
AMD's fix, **[#10766](https://github.com/ROCm/rocm-libraries/pull/10766), is merged** — I reviewed
it and the two edge cases I raised went into the maintainer's revision. Reproduction data and
harnesses are in [`emugemm-cdna/issue9985/`](https://github.com/Mazukiri/emugemm-cdna/tree/main/issue9985).

**[vllm-project/vllm](https://github.com/vllm-project/vllm/pulls?q=author%3AMazukiri)** — three
open bug-fix PRs ([#54338](https://github.com/vllm-project/vllm/pull/54338),
[#54341](https://github.com/vllm-project/vllm/pull/54341),
[#54342](https://github.com/vllm-project/vllm/pull/54342)). Each closes one place where two parts
of the tree disagree about what is supported — a KV-cache dtype that silently reinterprets the
model's bits, an FP8 format advertised but never written, and cache keys that collide across
multimodal and LoRA identities.

---

### Projects

#### ⚙️ [emugemm-cdna](https://github.com/Mazukiri/emugemm-cdna) — emulated-FP32 GEMM for AMD CDNA
Splits FP32 GEMM into three bf16 matrix-core products. **1.20–1.22× native rocBLAS FP32** on every
shape tested, at half its error constant. The part I care about is the **accuracy contract**: it
reports failure instead of silently under-delivering, and held **0 violations** across 15
well-conditioned and 10 adversarial cases (κ up to 1.1e6). The first AMD CDNA implementation of a
technique published only for NVIDIA. An early 2.1× collapsed to 1.21× once I tuned the baseline as
hard as my own kernel — the README documents that, because it is the more useful result.

#### 🚕 [Rider-Share](https://github.com/Mazukiri/Rider-Share) — distributed ride-sharing system
Go, gRPC and Kubernetes on a 3-node GKE cluster. **5,200 RPS at p99 42 ms and 0.01% errors** on
4 vCPU / 16 GB under k6, with Saga/Outbox orchestration holding consistency under injected
failures. A four-person project; I owned the matching engine and the load-test campaign.

#### ⚡ [Go-Redis](https://github.com/Mazukiri/Go-Redis) — Redis from scratch in Go
A single-threaded TCP server on epoll I/O multiplexing — a non-blocking event loop against the
C10k problem — with a SkipList sorted set and incrementally-resized hash maps holding Redis's
O(log N) contract over RESP. **49,000 ops/sec at 20 µs** average latency under `redis-benchmark`.

---

### Experience

**GPU Engineer Intern — Moreh** *(Jul 2026 – present)*
* Optimised a DeepSeek-V2 MLA + MoE inference engine in C/HIP on MI250: **1,076 → 1,745 tok/s
  (+62%)** on DeepSeek-V2-Lite and **327 → 802 tok/s (+145%)** on GLM-4.7, inside an accuracy gate.
* Moved the usable serving knee for gemma-4-31B **from 60.1 to 140.9 tok/s/GCD (+134%)**, scoring
  each trial against a roofline limit computed in advance.
* Root-caused two independent MoE sharding bugs that crashed vLLM GPT-OSS at data-parallel size 2.

**Software Engineer Intern — FPT Software** *(Jun 2025 – Sep 2025)*
* Built a Python/regex transpiler generating TypeScript schema validation, **adopted by 12
  engineers** at 300+ cases each daily.
* Cut master-data database read load **40%** with a Redis cache-aside layer.

---

### Tools

| | |
| :--- | :--- |
| **GPU** | ROCm/HIP · CUDA · OpenAI Triton · MFMA matrix cores · rocprofv3 / rocprof-compute · roofline analysis |
| **ML systems** | vLLM · SGLang · MoE · tensor/data parallelism · KV cache · INT8 &amp; BF16 quantization · PyTorch |
| **Languages** | C++ · C · Python · Go · SQL · TypeScript |
| **Systems** | Linux syscalls · epoll · TCP/IP · gRPC · Redis · Docker · Kubernetes · Prometheus |

---

### ICPC

* **Third Prize — ICPC Vietnam National Contest**, rank 56/440 (top 13%), Nov 2025
* **ICPC Asia Ho Chi Minh City Regional**, rank 32/120 (top 27%), Dec 2025
* **ICPC Asia Hanoi Regional**, rank 41/120 (top 34%), Dec 2024
