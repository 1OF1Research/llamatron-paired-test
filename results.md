# Paired before/after test — results

Run 2026-11-17/18 on the homebox `gqxamm`, under commitment.md (written before any generation; SHA-256 `054d51486f65d2465fd59dffa03b0ae4d1b6cfbe1d3b39d638d2c8226f9e205b`).
Raw outputs: `raw/plain-p1..p8.out/.err` and `raw/llamatron-p1..p8.out/.err`, byte-for-byte as the harness produced them. `extracted/` holds reading copies with the harness banner removed — the raw files are the record.

## The pair

- **Llamatron** — `/home/agent/hf-weekend/llamatron-keep/nemotron-split3b-imatrix.gguf`, 18,725,978,368 bytes, SHA-256 `99d564ee23b82f3fc1cc15bb3a450f5c5694df2f3e39814dfb999835dcd4b8b4`.
- **Plain copy** — bartowski/nvidia_Nemotron-3-Nano-30B-A3B-GGUF, `nvidia_Nemotron-3-Nano-30B-A3B-Q3_K_M.gguf`, 18,987,122,912 bytes, SHA-256 verified against the repository's own metadata before the run: `1b282e9ddf8622bf318dd9fadcc76cea1bac8815fbbef5bd76581336ba3f27b0`. A standard plain 3-bit quant of the same stock model — no importance matrix, no cache repair. The card's own plain-copy file and the stock checkpoint are no longer on this machine (the checkpoint directory was removed earlier; only broken symlinks remain), so a fresh standard quant stands in, declared in the commitment before the run. This is the one substitution in the test, and it is a substitution of file, not of model or recipe class.

## Harness

llama-cli (house build, version 1.0.0-dev, commit b1b770d), identical for both: `-ngl 10 --cpu-moe -c 2048 -n 512 --temp 0 --seed 1234`, plain completion mode, one generation per prompt per model, no re-rolls, no retries needed (all 16 runs exited rc=0). The runner waited for a clear machine before every run; the batch itself waited ~55 minutes at the start while another lane's generation finished. Generation speeds below are the harness's own counters; the box carried other house work during the night, so treat speeds as same-night indicative, not laboratory values.

## Scoreboard — answer delivered inside the 512-token window, and right

| # | Prompt | Plain copy | Llamatron | Verdict |
|---|--------|-----------|-----------|---------|
| P1 | Logic puzzle (gold/silver/empty) | No answer — still enumerating cases at the cap | No answer — had ruled out one case, was working the right branch at the cap | Tie, no answer either side |
| P2 | Money maths | **$721.12**, steps correct | **$721.12**, steps correct | Tie, both right |
| P3 | Moon landing, with a trap | **1969, Neil Armstrong** | **1969, Neil Armstrong** | Tie, both right |
| P4 | Median function | Correct — verified by running it | Correct — verified by running it | Tie, both right |
| P5 | Four bullets, six words each, no commas | No answer — spent the window counting words | No answer — all four bullets drafted inside its thinking, still verifying counts at the cap | Tie, no answer either side |
| P6 | Three-sentence summary with both numbers | No answer — stalled in thinking | No answer — stalled in thinking, on the same snag | Tie, no answer either side |
| P7 | Why the sky is blue, ≤3 sentences | **Pass** — two sentences, correct, stopped at the ask | **Pass** — three sentences, correct, plus a sunset aside | Tie on pass; small style edge to the plain copy |
| P8 | Story opening, ~120 words | No answer — draft existed in its thinking, still counting words at the cap | No answer — draft existed in its thinking, still counting words at the cap | Tie, no answer either side |

**Total: 4 prompts answered correctly by both, 4 answered by neither, 0 prompts split the pair.**

## What the smaller version gives up — the honest answer

On this set, at this window: **nothing measurable.** Every prompt the plain copy answered, Llamatron answered too, with the same correctness (the maths matches to the cent; both code samples pass the same checks when run). Every prompt Llamatron missed, the plain copy missed too, in the same way. The one difference of any kind ran *against* Llamatron: on P7 the plain copy stopped at exactly two sentences while Llamatron filled its full allowance and added a sentence about sunsets that nobody asked for.

The shared failure shape is the finding inside the finding: on P1, P5, P6 and P8, both models spent the entire 512-token window thinking and never shipped the answer — on P5 and P8, both had effectively finished drafts sitting inside their thinking and ran out of room while polishing them. A bigger window, not a bigger model, is what those four prompts wanted. That is a round-two test, and the draft thread below invites it.

## Speed (harness counters, generation tokens/sec) and wall time

| # | Plain gen t/s | Llamatron gen t/s | Plain wall | Llamatron wall |
|---|------|------|------|------|
| P1 | 1.7 | 2.2 | 372s | 285s |
| P2 | 1.8 | 2.1 | 331s | 272s |
| P3 | 1.7 | 2.0 | 167s | 140s |
| P4 | 1.4 | 1.6 | 361s | 280s |
| P5 | 2.0 | 1.8 | 311s | 340s |
| P6 | 1.8 | 0.8 | 343s | 713s |
| P7 | 0.7 | 1.7 | 389s | 157s |
| P8 | 2.0 | 2.3 | 342s | 277s |
| **Total wall** | | | **2,616s** | **2,464s** |

Speed is a wash: median generation 1.75 t/s (plain) vs 1.9 t/s (Llamatron), with wide swings both ways from other work on the machine (Llamatron's P6 ran at 0.8 t/s under contention; the plain copy's P7 at 0.7). No speed claim survives these numbers, in either direction.

## Faults and limits, named

- **Comparator provenance** (above): a fresh third-party plain quant stands in for the card's own plain copy, which no longer exists on the box. Declared in the commitment, before the run.
- **The window was 512 tokens, plain completion mode, no chat template.** Both models spend tokens on visible thinking in this harness. A chat-templated run, or a larger window, could separate them; this test answers the question as asked — same prompts, same settings, side by side.
- **P6's wording was partly the tester's fault.** The prompt said "both numbers" while the passage also contains years (1911, 1958, 1974); both models stalled on exactly that ambiguity. The grading key (2,400 and 31,000 — the two counted quantities) stands, but the snag is recorded here as a prompt-design lesson, not a model fault.
- **P3 footnote, both sides:** each model's thinking trace names the first person in space while reasoning about the trap; neither final answer does. The answers as delivered are clean.
- **Box contention:** speeds varied with other house work. The wait-for-a-clear-machine rule kept generations from overlapping; it could not clear downloads and background jobs.

## Time

Comparator download (18,987,122,912 bytes at ~1–2 MB/s, shared with another lane's download): ~3¼ hours. Wait for a clear machine: ~55 minutes. Generation: 5,080 seconds (~85 minutes) across 16 runs. End to end, first check to last output: ~5¾ hours.

## Public home for the full outputs

Recommended: a public gist under the house GitHub identity **1OF1Research** holding commitment.md, the eight prompt files, all 32 raw output files, and this results file — one stable link the Bluesky thread can point to, fully checkable. Second home: a post in the model page's discussion, where the card's own readers already are. Nothing has been published from this package yet.
