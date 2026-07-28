## What's here

Small, dependency-light tools. Most of them exist because the obvious library was
heavier than the problem, so the primitive got written by hand and tested against
the spec's own vectors.

Three threads run through the repositories, and they cross more than they look like
they should.

### On-chain

| Repository | What it does |
|---|---|
| [merkle-drop](https://github.com/roxiproject/merkle-drop) | Merkle airdrop toolkit — `MerkleDistributor.sol` with a packed claimed bitmap, plus a CLI that turns `address,amount` CSV into a root and per-claimant proofs. |
| [streamvault](https://github.com/roxiproject/streamvault) | Continuous ERC-20 payment streams. No owner, no proxy, no upgrade path — the deployed bytecode is the whole protocol. |
| [evm-wallet-watch](https://github.com/roxiproject/evm-wallet-watch) | Follow addresses across EVM chains: transfers, approvals, NFT activity, decoded from raw logs. |
| [bridgewatch](https://github.com/roxiproject/bridgewatch) | Reconciles L1↔L2 bridge messages on OP Stack and Arbitrum so you can find withdrawals that are stuck, unproven, or sitting unclaimed. |
| [solana-lens](https://github.com/roxiproject/solana-lens) | Solana account inspector over public JSON-RPC, with hand-written base58 and SPL Token layout decoding instead of `solana-sdk`. |

`merkle-drop`, `streamvault` and `bridgewatch` share the same hand-written
`keccak256`, ABI encoder and EIP-55 checksum code, each re-derived and re-tested per
repository rather than pulled from a common package — the constraint being that
none of them take a runtime dependency. `evm-wallet-watch` is the read side for the
first two: the events those contracts emit are the events it decodes.

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
| [costbasis](https://github.com/roxiproject/costbasis) | Cost-basis and capital-gains reporting — FIFO/LIFO/HIFO lot matching, `Decimal` throughout, Form 8949 output. Not tax advice. |

`costbasis` is where the on-chain thread lands: disposals have to be accounted for
eventually, and `evm-wallet-watch` output is the natural input.

### Shape

Nearly all of these follow the same layout — a pure core that takes plain data and
returns plain data, and a thin edge that touches the network or the filesystem. It
makes the test suites offline by construction, which is the actual reason for it.
