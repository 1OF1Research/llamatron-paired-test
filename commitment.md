# Paired before/after test — commitment (written before any generation)

- Written: 2026-11-17 ~21:12 CST, before a single token of this test was generated.
- Why: @waterreptile19.bsky.social asked, under the Llamatron thread, for before/after answers on the same prompts, "to see what the smaller version gives up, if anything." We publicly promised a side-by-side run. They named no prompts, so the house chose them — which is why this design is committed in writing, with hashes, before any output exists.
- Machine: homebox `gqxamm` (hostname asserted by SSH read), GTX 1650 Max-Q 4 GB, 16 GB RAM, 8 threads.

## The two models

**After — Llamatron (the house build)**
- File: `/home/agent/hf-weekend/llamatron-keep/nemotron-split3b-imatrix.gguf`
- Size: 18,725,978,368 bytes
- SHA-256: `99d564ee23b82f3fc1cc15bb3a450f5c5694df2f3e39814dfb999835dcd4b8b4`

**Before — a plain copy of the same stock model**
- Provenance, stated plainly: the card's own plain-copy file, and the stock BF16 checkpoint it was built from, are no longer on this machine (checked 2026-11-17: the checkpoint directory was removed at some earlier point; only broken symlinks remain). Nothing was substituted silently.
- The comparator is therefore a standard plain quant of the same stock model (NVIDIA Nemotron-3-Nano-30B-A3B), downloaded from Hugging Face: bartowski/nvidia_Nemotron-3-Nano-30B-A3B-GGUF, file `nvidia_Nemotron-3-Nano-30B-A3B-Q3_K_M.gguf` — the plain static 3-bit recipe, no importance matrix, no cache repair, none of the house's changes.
- Expected size: 18,987,122,912 bytes. Expected SHA-256 (from the repository's own metadata): `1b282e9ddf8622bf318dd9fadcc76cea1bac8815fbbef5bd76581336ba3f27b0` — verified against the downloaded file before any generation; the verified value is recorded in results.md.
- Known asymmetry, declared in advance: this comparator is a third party's quant of the stock model, not the house's own plain copy from the card. The comparison class is the same (plain 3-bit quant of the stock model, near-identical file size), and every public word about this test will carry that provenance.

## Harness — identical for both models

- Binary: `/home/agent/llama-v1/build/bin/llama-cli`, version `1.0.0-dev (build 1, commit b1b770d)`.
- Command shape: `llama-cli -m <model> -ngl 10 --cpu-moe -c 2048 -n 512 --temp 0 --seed 1234 -f <prompt file>`, stdin closed, plain completion mode (no chat template applied — the same harness shape the house's own Llamatron demo runs used), 1500-second timeout per run.
- Order: plain copy first (P1–P8), then Llamatron (P1–P8). Before every run, the runner waits until no other llama-cli process is active on the box.
- One generation per prompt per model. First output stands. No re-rolls. If a run fails as a harness error (crash, timeout, load failure), the error is recorded and the run gets exactly one retry; a weak or wrong answer is never a reason to retry.
- Outputs are captured raw and unedited — full stdout and stderr per run, plus wall-clock seconds per run.

## The prompts (exact bytes in prompts/p1.txt … p8.txt)

SHA-256 of each prompt file:
- p1.txt `b049bd528e6a88b14f7adcd99da947e46724d2e1def810100fcc90c07b87476e`
- p2.txt `584642770543eaf20aea6a1efcfae0a9baefc7b6f07f9321dbdde6d2fbd2bf7e`
- p3.txt `476c809adcc66ae9df7216d04500f2450ab90e1dc6bfe53e308422a1d52d3f07`
- p4.txt `97d2f3a828f9503c801c202e71497175b6a0be5966328b0fdacb5341ccbd2649`
- p5.txt `40be4e436d0cf0ba066481a05b4ac38c593bb790f1597b0c4a40ce621e6b1f75`
- p6.txt `d40112f13fde52af7d78fbece9a29ee987e23ad45eb745e6559b420d81de0f30`
- p7.txt `bfc4a39060910c00fe3064c0d9cf8c3e3daf462643101befc9a0f570e35ce0f4`
- p8.txt `2e70536e83db33c606d9a95ae103edf9dd11fde91180b1b861fbb3db39601267`

Kinds: P1 multi-step reasoning · P2 math word problem · P3 factual recall with a trap · P4 code writing · P5 instruction-following with hard constraints · P6 summarization of a fixed passage · P7 short explanation where stopping matters · P8 open writing.

## Grading keys (fixed now, before outputs exist)

- P1: Gold in A, silver in C, B empty — the only assignment under which exactly one statement is true.
- P2: $840 × 0.85 = $714.00; × 1.08 = $771.12; − $50 = **$721.12**.
- P3: 1969, Neil Armstrong. Naming Yuri Gagarin (first in space) fails the trap.
- P4: function returns the true median (mean of the two middle values for even counts), None on empty, no imports. The code will be run, not just read.
- P5: exactly 4 bullet points, exactly 6 words in each, zero commas. Counted mechanically.
- P6: exactly 3 sentences, and both numbers from the passage appear (2,400 and 31,000).
- P7: at most 3 sentences, and the explanation is the real one (sunlight scattered by air; blue scatters most).
- P8: roughly 120 words (90–150 accepted), reads as a story opening, and does not explain the letter.

## Reporting rule

Where Llamatron is worse gets the same prominence as where it is better. A tie or a loss is a result, not a problem. Nothing in results.md may claim past what these 16 raw outputs show.
