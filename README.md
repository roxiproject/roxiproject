## What's here

Small, dependency-light tools. Most of them exist because the obvious library was
heavier than the problem, so the primitive got written by hand and tested against
the spec's own vectors.

Four threads run through the repositories, and they cross more than they look like
they should.

### On-chain

| Repository | What it does |
|---|---|
| [merkle-drop](https://github.com/roxiproject/merkle-drop) | Merkle airdrop toolkit — `MerkleDistributor.sol` with a packed claimed bitmap, plus a CLI that turns `address,amount` CSV into a root and per-claimant proofs. |
| [streamvault](https://github.com/roxiproject/streamvault) | Continuous ERC-20 payment streams. No owner, no proxy, no upgrade path — the deployed bytecode is the whole protocol. |
| [evm-wallet-watch](https://github.com/roxiproject/evm-wallet-watch) | Follow addresses across EVM chains: transfers, approvals, NFT activity, decoded from raw logs. |
| [bridgewatch](https://github.com/roxiproject/bridgewatch) | Reconciles L1↔L2 bridge messages across OP Stack and Arbitrum Orbit chains — including Robinhood Chain — so you can find withdrawals that are stuck, unproven, or sitting unclaimed. |
| [solana-lens](https://github.com/roxiproject/solana-lens) | Solana account inspector over public JSON-RPC, with hand-written base58 and SPL Token layout decoding instead of `solana-sdk`. |
| [costbasis](https://github.com/roxiproject/costbasis) | Cost-basis and capital-gains reporting — FIFO/LIFO/HIFO lot matching, `Decimal` throughout, Form 8949 output. Not tax advice. |

`merkle-drop`, `streamvault` and `bridgewatch` share the same hand-written
`keccak256`, ABI encoder and EIP-55 checksum code, each re-derived and re-tested per
repository rather than pulled from a common package — the constraint being that
none of them take a runtime dependency. `evm-wallet-watch` is the read side for the
first two: the events those contracts emit are the events it decodes. `costbasis`
is where the on-chain thread lands: disposals have to be accounted for eventually,
and `evm-wallet-watch` output is the natural input. `bridgewatch`'s Arbitrum Orbit
adapter is what a Robinhood Chain analytics tool like `stocktoken` (below) sits on
top of for L1↔L2 message reconciliation.

### LLM tooling

| Repository | What it does |
|---|---|
| [token-meter](https://github.com/roxiproject/token-meter) | What will this prompt, file, or repository cost — across models, before you spend anything. |
| [promptcheck](https://github.com/roxiproject/promptcheck) | Regression testing for prompts. YAML cases, real providers, results in SQLite so you can see when a prompt started failing. |
| [semantic-notes](https://github.com/roxiproject/semantic-notes) | Local semantic search over a Markdown notes directory. |

`token-meter` and `promptcheck` are the two halves of prompt CI: one catches the
prompt that got more expensive, the other catches the prompt that got worse. They
tend to run in the same job.

### Data and text

| Repository | What it does |
|---|---|
| [logquery](https://github.com/roxiproject/logquery) | A query language over structured log streams — filters, functions, aggregation. |
| [feedmerge](https://github.com/roxiproject/feedmerge) | Feed aggregation with full-text BM25 search and an append-only, crash-safe store. |
| [contribution-atlas](https://github.com/roxiproject/contribution-atlas) | Renders a GitHub contribution calendar as a terminal heatmap or an SVG, with the streak and language numbers the profile page leaves out. |

### ML and NLP research

Attention mechanics, tokenization, embeddings, and preference-tuning, each
correctness-checked against a mechanical reference rather than trusted by
construction: backprop against finite-difference gradients, cached decoding
against a full forward pass, BPE merges against a byte-exact round trip.

| Repository | What it does |
|---|---|
| [attention](https://github.com/roxiproject/attention) | Attention and KV-cache implementations, verified by matching cached token-by-token decoding to a full forward pass bit-for-bit. |
| [attention-probe-kit](https://github.com/roxiproject/attention-probe-kit) | Instruments attention heads to extract and visualize what a probe finds them attending to. |
| [probe-experiments](https://github.com/roxiproject/probe-experiments) | Linear and non-linear probing experiments over model activations. |
| [embed-bench](https://github.com/roxiproject/embed-bench) | Embedding quality benchmarking: nearest-neighbor recovery under k-means, LSH, and IVF approximate search. |
| [corpus-kit](https://github.com/roxiproject/corpus-kit) | Corpus construction and cleaning utilities. |
| [corpus-bench](https://github.com/roxiproject/corpus-bench) | Benchmarks corpus-processing throughput and quality. |
| [corpus-tokenizer-kit](https://github.com/roxiproject/corpus-tokenizer-kit) | Byte-level BPE tokenizer, rank-ordered merges, exact round-trip decode verified with property-based tests over random byte strings. |
| [corpus-corpus.py](https://github.com/roxiproject/corpus-corpus.py) | Near-duplicate detection (MinHash/shingling) and language ID (Cavnar-Trenkle character n-grams) over large text corpora. |
| [lora-kit](https://github.com/roxiproject/lora-kit) | LoRA fine-tuning utilities, gradient-checked against dense-layer baselines. |
| [rlhf-experiments](https://github.com/roxiproject/rlhf-experiments) | Bradley-Terry preference loss, REINFORCE, GAE, and a clipped PPO-lite surrogate objective, each gradient-verified. |
| [rlhf-distill-experiments](https://github.com/roxiproject/rlhf-distill-experiments) | Distillation from a preference-tuned policy into a smaller model. |

`corpus-kit`, `corpus-bench`, `corpus-tokenizer-kit`, and `corpus-corpus.py` are
the pipeline a tokenizer actually needs: build the corpus, clean it, tokenize it,
benchmark the result. `attention` and `attention-probe-kit` feed `probe-experiments`
the activations it inspects. `rlhf-experiments` is the training loop;
`rlhf-distill-experiments` is what happens to the resulting policy afterward.

These repositories carry longer histories than the rest of the account and some
still show early scaffolding above the real modules that were built into them —
the commit log is left intact rather than rewritten.

### Shape

Nearly all of these follow the same layout — a pure core that takes plain data and
returns plain data, and a thin edge that touches the network or the filesystem. It
makes the test suites offline by construction, which is the actual reason for it.
