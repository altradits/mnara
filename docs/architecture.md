# Architecture

Mnara is a modular monolith with ports and adapters. Consensus code is pure functions with no I/O. The domain decides status. Adapters fetch facts.

## Shape

Bitcoin P2P peers send headers over TCP to `internal/p2p`. It offers a HeaderSource port. `internal/consensus` holds pure checks. `internal/chain` holds the header tree and the best work tip. The owner LND talks over gRPC to `internal/lnd`. It offers a NodeView port. `internal/witness` compares the chain view with the node view and sets status. `internal/store` keeps headers, peers, and events in bbolt. `internal/web` serves the status page. `internal/alert` sends webhooks, runs commands, and maps exit codes.

## Packages

- `cmd/mnara`: run, status, check-macaroon, version, headers.
- `internal/config`: flags, env, mnara.conf, validation.
- `internal/consensus`: header codec, hash, compact target, proof of work, retarget, median time past.
- `internal/merkle`: root, branch, branch verify, mutation check.
- `internal/chain`: header tree, best work tip, reorg events, block locator.
- `internal/p2p`: peer lifecycle, handshake, address book, netgroups, ban scores.
- `internal/filters`: filter header chain and GCS match (milestone 2).
- `internal/anchor`: channel anchors and spend watch (milestone 2).
- `internal/lnd`: read-only LND adapter and macaroon guard.
- `internal/core`: Bitcoin Core adapter (milestone 3).
- `internal/witness`: status rules and event log.
- `internal/stats`: whitepaper section 11 maths and Poisson silence.
- `internal/store`: bbolt buckets for headers, peers, events, anchors.
- `internal/web`: status page, settings view, events.
- `internal/alert`: webhook, command, exit codes.
- `internal/i18n`: catalog loader, plurals, locale choice.

## Storage

One bbolt file, `mnara.db`, mode 0600. Buckets: meta, headers, best, peers, events, anchors, filterheaders. Headers are 80 bytes each. That is about 4.2 MB per year. About 930,000 headers today is about 75 MB plus index.

## Build and test

Build with `go build`. Unit tests run with `go test -race ./...`. Consensus tests use vectors from real mainnet headers and from Bitcoin Core. Fuzz tests cover header decode, compact target, Merkle, and the P2P reader. Integration tests use regtest with three bitcoind nodes and lnd on neutrino.
