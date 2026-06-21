# Strix Halo + LLMs — a field series on single-APU inference

*By Pradeep Nallimelli — AMD Field AI Enthusiast*

Running large language models on the **AMD Ryzen AI MAX+ 395 (Strix Halo)** APU: one
Radeon 8060S iGPU (gfx1151), 128 GB unified LPDDR5X, and an XDNA NPU. The hook of
this part is unusual for an integrated GPU — with unified memory you can fit a
**70B Q4 model on the iGPU** with no discrete card.

Sanitized for public use — no internal hostnames, IPs, tokens, or org-specific paths.

| Part | Title | Theme |
|---|---|---|
| 1 | [ROCm vs Vulkan on Strix Halo](./part1-strix-halo-rocm-vs-vulkan.html) | Two backends head-to-head; single-user vs concurrent; 70B on an iGPU |
| 2 | [The memory frontier — a 120B on one iGPU](./part2-strix-halo-gptoss-120b.html) | gpt-oss-120B MXFP4 MoE on the iGPU (no reboot); ROCm vs Vulkan single-user + concurrency |
| 2.5 | *Past the default pool — 235B & the reboot gate* (planned) | GTT-pool tuning beyond 96 GiB, Qwen3-235B-A22B |
| 3 | *Bringing in the NPU* (planned) | XDNA / Lemonade hybrid execution across CPU + iGPU + NPU |

## Headline numbers (Part 1)

- Same APU, same 30B-A3B MoE model, two runtimes: **Vulkan wins single-user decode**; **ROCm wins at ≥16 concurrent streams**.
- A **70B Q4** dense model runs entirely on the integrated GPU through the 96 GB GTT unified-memory pool — no discrete accelerator.

## Headline numbers (Part 2 — gpt-oss-120B)

- A **116.8B-parameter** MoE model (MXFP4, 59 GiB) loads **entirely onto the iGPU** at `-ngl 99` via the 96 GiB unified pool — **no reboot, no grub/BIOS change**.
- Single-user decode **~56 tok/s** (Vulkan, FA on); Vulkan prefills **3.2×** faster than ROCm (569 vs 179 tok/s).
- Decode-bound concurrency crossover at **4 streams**: ROCm reaches **227 tok/s @ 16 streams (+78%)** while Vulkan plateaus.

## Reproducibility

Each post documents the full setup: OS/kernel, ROCm version, llama.cpp build commit and
flags, model + quantization, and the exact benchmark commands, so a reader can reproduce
every number on their own Strix Halo box.
