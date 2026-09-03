<h1 align="center">Mai Hoàng Đức</h1>
<p align="center"><i>I make GPUs go faster, then spend twice as long checking I didn't just imagine it.</i></p>

<p align="center">
  <a href="https://linkedin.com/in/hoangducmai">
    <img src="https://img.shields.io/badge/LinkedIn-hoangducmai-blue?style=flat-square&logo=linkedin" alt="LinkedIn" />
  </a>
  <a href="mailto:maihoangduc04@gmail.com">
    <img src="https://img.shields.io/badge/Email-maihoangduc04-red?style=flat-square&logo=gmail" alt="Email" />
  </a>
</p>

Final-year CS student at VNU University of Science in Hanoi, graduating **July 2027**.
Right now I'm a GPU engineer intern at **Moreh**, a Korean AI-infrastructure company —
which in practice means I spend my days on AMD MI250s asking why some number isn't the
number it ought to be.

Almost everything below started as *"hang on, that can't be right."* Usually something
was wrong. Sometimes the thing that was wrong was me, and those turned out to be the
ones worth writing down.

---

### 🔍 A library that was losing to itself

rocBLAS ships a pile of GEMM kernels and picks one for you. I measured **7,009 shapes**
on an MI250, and the kernel it picks loses to one *already sitting inside the same
library* on **75%** of them — by 10% or more on half. Median 1.10×, 9.2× at p99. Same
story again on MI300X.

Filed as **[ROCm/rocm-libraries#9985](https://github.com/ROCm/rocm-libraries/issues/9985)**.
AMD put two engineers on it, and the Tensile fix it needed shipped in
**[#10766](https://github.com/ROCm/rocm-libraries/pull/10766) — merged**. I reviewed that
PR, caught a sentinel value that could still slip past the parser's own filter, and both
cases went in with it. I'm still a bit delighted that worked.

### 😅 A 2.1× speedup that was really 1.21×

My [emugemm-cdna](https://github.com/Mazukiri/emugemm-cdna) library splits FP32 into
three bf16 matrix-core products. Early on it looked like a **2.1×** win over native FP32.
It was **1.21×**.

I had tuned my own kernel with great care and left rocBLAS sitting on its default
kernel — so I'd spent a week beating a strawman I built myself. (The default kernel being
bad is, with hindsight, the entire subject of #9985. I found that out the embarrassing
way first.) The honest number is **1.20–1.22×**, and the README leads with the mistake,
because it's the most useful thing in the repo.

The part I'm actually proud of isn't the speed — it's the accuracy contract. Ask it for a
precision and it either delivers or **tells you it can't**, instead of quietly returning
something worse. Zero violations across 15 well-conditioned cases and 10 deliberately
nasty ones.

### 🤦 The time my test harness was the broken thing

Mid-way through a throughput campaign on gemma-4-31B, I added a correctness gate. It
promptly reported that the model had lost its mind:

```
"The capital of France is"             ->  " France is France is France is…"
"In 1969, humans first landed on the"  ->  "ว theว theว theว the…"
```

Two possible readings, and the difference was the whole campaign: either the engine was
numerically broken and every number I had was void, or *I* was holding it wrong. It was
the second one — I was posting to the completions endpoint without a chat template. Same
server, asked properly, answers `Paris`, `34`, and `Moon.`

Every throughput number survived. I have never been so happy to be the problem. The
campaign itself moved the usable serving knee **from 60.1 to 140.9 tok/s per GCD**.

### 🧵 Three warps, one buffer, no fence

A sampler on ROCm returned a **different answer every time for identical input** — 30
calls, 30 different masks. The Triton kernel wrote a scratch row and read it back across
warps with nothing in between. Five `tl.debug_barrier()` calls and it was deterministic
again. Then I wrote an AST scan over **583 Triton kernels** looking for the same shape of
bug elsewhere, found one suspect, measured it, and it was innocent. Negative results count.

Separately, the same habit of chasing "two places in one codebase disagree about
what's supported" produced **[three open PRs to vLLM](https://github.com/vllm-project/vllm/pulls?q=author%3AMazukiri)**:
a KV-cache dtype that silently reinterprets the model's bits, an FP8 format the code
advertises but never writes, and cache keys that collide across multimodal and LoRA
identities.

---

### 🛠️ Other things I've built

**[Rider-Share](https://github.com/Mazukiri/Rider-Share)** — a distributed ride-sharing
system in Go and gRPC on Kubernetes, with three friends. **5,200 RPS at p99 42 ms.** I
owned the matching engine, where `pprof` eventually told me the bottleneck was a mutex I'd
written myself.

**[Go-Redis](https://github.com/Mazukiri/Go-Redis)** — Redis from scratch, because reading
about epoll is not the same as using it. Event loop, RESP, SkipList sorted sets,
**49,000 ops/sec at 20 µs**.

**Moreh, day job** — ported a DeepSeek-V2 MLA + MoE engine to HIP with two colleagues and
took it **from 1,076 to 1,745 tok/s**; the interesting part was a profiler telling me the
matrix cores were idle at 28% while L2 was pinned at 95%, which turned out to be a tile
shape and nothing else.

---

### 🧰 Tools I reach for

`ROCm/HIP` · `CUDA` · `Triton` · `C++` · `C` · `Go` · `Python` · `vLLM` · `SGLang` ·
`rocprof-compute` · `Linux` · `Kubernetes` · `gRPC` · and `rocprofv3` far more often than
I expected to.

### 🏅 ICPC

Third Prize at the **ICPC Vietnam National Contest** (56/440), plus Asia Regionals in
Ho Chi Minh City (32/120) and Hanoi (41/120). Competitive programming is where I learned
that being fast and being right are different skills, and only one of them scores.

### 📚 Off the clock

Murakami and Charlotte Brontë. Currently learning Chinese, slowly and with great respect
for anyone who has finished. I'm writing a book I may never publish. And I keep a running
list of measurements I don't believe yet, which is either a hobby or a personality trait.
