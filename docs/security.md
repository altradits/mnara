# Security

## Assets, in order

1. Correctness of "OK". A false OK is the worst failure. A false alarm is cheap.
2. Owner privacy: channel points, node identity, the fact that this IP runs Lightning.
3. The LND node. Mnara must never be a path to it.
4. Availability of the witness.

## Threats and answers

- T1: Mnara itself is eclipsed. Answer: netgroup diversity, peers that differ from LND peers, rotation, extra dials on silence, quorum by netgroups, Cannot witness instead of OK when silent. An attacker who controls the whole owner network path can still fool both. That limit is documented.
- T2: a peer sends invalid or low-work headers. Answer: every check in docs/p2p-and-headers.md runs in-house. Ban score. Low-work branches never hit disk.
- T3: header flood. Answer: orphan cap, per-peer rate limit, work threshold before disk writes.
- T4: a peer sends a lying filter that hides a spend. Answer: filter headers must agree across peers in distinct netgroups. Partial checks against the block. On mismatch, fetch the block.
- T5: a stolen Mnara host is used against LND. Answer: read-only macaroon, enforced, no override.
- T6: privacy leak to peers. Answer: no address or outpoint queries. relay is false. Whole filters and blocks only. Funding blocks are fetched once and cached.
- T7: status page abuse. Answer: binds 127.0.0.1 by default. No write actions on the page. Strict CSP. No secrets rendered.
- T8: webhook leaks. Answer: off by default. Body holds heights and status only, never channel points. HTTPS unless loopback.
- T9: wrong network. Answer: params come from config. Genesis hash is checked on each peer. LND network is checked at start.
- T10: host clock is wrong. Answer: if the best tip time is more than 2 hours ahead of local time, show Cannot witness and log a clock event.
- T11: supply chain. Answer: deps equal LND pins, go.sum, weekly Dependabot, govulncheck, actions pinned by full SHA.

## Minimum limits

1. Read-only macaroon, enforced, no override.
2. Localhost bind. No write endpoints on the web UI.
3. No outbound traffic except peers, DNS seeds, LND, Core, and the set webhook.
4. Every header verified in-house before use.
5. Quorum by netgroups before any OK.
