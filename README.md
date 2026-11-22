# Mnara

An independent witness of the Bitcoin chain for your Lightning node.

## Status

This project is early. There is no code yet.

Start with the pinned "Start here" issue. It lists the first tasks in learning order.

## The problem

A Lightning node cannot tell, on its own, whether its view of the Bitcoin blockchain is current and on the most-work chain. When it is wrong, nothing tells the owner. A wrong or late view of the chain is how Lightning funds are stolen.

Lightning safety depends on time measured in blocks. A node must see a cheating close or an expiring payment on chain before a block height deadline, and respond. If an attacker controls which blocks the node sees, the attacker controls the node clock.

Time dilation attacks use this gap. Riard and Naumenko showed that an attacker who eclipses a Lightning node and delays block delivery can steal channel funds. Keeping a victim eclipsed for about 2 hours is enough for one variant, and 24 hours for another. Light clients are easier to eclipse. Source: A. Riard, G. Naumenko, "Time-Dilation Attacks on the Lightning Network", arXiv:2006.01418 (2020), https://arxiv.org/abs/2006.01418, and the summary at https://discrete-blog.github.io/time-dilation.

Eclipse attacks on the peer-to-peer network are practical. Source: E. Heilman, A. Kendler, A. Zohar, S. Goldberg, "Eclipse Attacks on Bitcoin's Peer-to-Peer Network", USENIX Security 2015, https://www.usenix.org/conference/usenixsecurity15/technical-sessions/presentation/heilman. Partition by routing attacks is practical too. Source: M. Apostolaki, A. Zohar, L. Vanbever, "Hijacking Bitcoin: Routing Attacks on Cryptocurrencies", IEEE S&P 2017, https://btc-hijack.ethz.ch/.

Plain failure hurts too, with no attacker. A stuck backend, a dead peer set, or a laptop that slept leaves `lncli getinfo` showing an old height. LND `synced_to_chain` compares LND to its own backend, so a backend that is itself behind still says synced.

## Who has this problem

Every Lightning node operator. Most of all those on a light chain backend: LND with neutrino (BIP157/158 compact filters), which many mobile and home nodes run. Also home nodes with few peers, behind one ISP, or on a network an attacker can influence.

## What Mnara does

Mnara is a small Go daemon. It syncs Bitcoin headers itself from diverse P2P peers. It verifies every header from first principles: double SHA-256, the compact target, proof of work, the difficulty retarget, median time past, and cumulative chain work. It then witnesses whether your LND node sees the real, current, most-work chain. It asks LND only read questions, with a read-only macaroon.

It shows one status page: OK, Behind, Different chain, or Cannot witness. It can run a local command, or post to a webhook you control, when the status changes.

## What Mnara is not

Mnara is not a wallet. It never signs, never broadcasts, never holds keys, never moves funds.

Mnara is not a watchtower.

Mnara is not a full node. It verifies headers and Merkle proofs. It does not check scripts or the UTXO set.

One instance watches one owner node. It is an independent second path to the network plus an alarm. It is not a defence by itself.

## Tech stack

- Go 1.26.10 (same as LND v0.21.6-beta).
- btcd wire and chaincfg for the P2P message codec only, chainhash, btcutil gcs, lnrpc and macaroons, grpc, bbolt, golang.org/x/text. Versions copied from LND v0.21.6-beta's go.mod.
- All consensus, Merkle, chain and peer logic written in-house.
- btcd/blockchain only in tests as a cross-check.
- bbolt storage in one file.
- CLI plus status: `mnara` commands and a local status page.
- regtest/signet integration tests with bitcoind and lnd on neutrino.

## Built on the whitepaper

Bitcoin: A Peer-to-Peer Electronic Cash System, https://bitcoin.org/bitcoin.pdf. Mnara code follows these sections. See docs/whitepaper-map.md for the full map.

| Section | Name | In Mnara |
|---|---|---|
| 2 | Transactions | Used. Funding transaction parsing and outpoint match (milestone 2). |
| 3 | Timestamp Server | Used. Header chain, median time past, future time limit. |
| 4 | Proof-of-Work | Used. Double SHA-256, nBits, proof of work, retarget, chain work. |
| 5 | Network | Used. P2P handshake, header sync, most-work chain choice. |
| 6 | Incentive | Not used. |
| 7 | Reclaiming Disk Space | Used. Merkle root, branch storage, 80-byte headers. |
| 8 | Simplified Payment Verification | Used. Header chain plus alerts for your own node. |
| 9 | Combining and Splitting Value | Not used. |
| 10 | Privacy | Used. No address queries. Whole filters and blocks only. |
| 11 | Calculations | Used. Attacker success maths and the silence alarm. |

## Requirements

- Go 1.26.10.
- An LND node.
- Internet access to Bitcoin P2P port 8333.

## Install

No release yet. This section will show install steps when code lands.

## LND setup

Mnara will need a read-only macaroon. The planned setup is in docs/lnd.md.

## Run

No binary yet. The planned commands are `mnara run` and `mnara status`. See docs/architecture.md.

## How verification works

Mnara checks every header itself before it trusts it. See docs/p2p-and-headers.md and docs/whitepaper-map.md.

## Develop

No code yet. Read CONTRIBUTING.md and the "Start here" issue. The planned checks are `make test`, `gofmt`, `go vet`, and `staticcheck`.

## Languages

English first, then Kiswahili. See docs/i18n.md. New languages are welcome if you speak them.

## Contributing

See CONTRIBUTING.md. Code of conduct: CODE_OF_CONDUCT.md.

## Security

See SECURITY.md. Do not open public issues for vulnerabilities.

## Licence

MIT. See LICENSE.
