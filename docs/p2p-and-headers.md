# P2P and headers

Mnara speaks Bitcoin P2P directly. It sends version and verack, then getheaders, and reads headers. It uses btcd wire only to encode and decode messages. All checks are in-house.

## Header checks

For every header, in order:

1. Decode 80 bytes: version, prev hash, Merkle root, time, bits, nonce.
2. Hash is SHA256(SHA256(header)).
3. Target is CompactToTarget(bits). Reject negative, overflow, zero, or above powLimit.
4. Hash must be at most target.
5. Prev must be a known header, or genesis. Unknown parents trigger a locator request. Orphan storage has a small cap.
6. Bits must equal NextTarget of the parent chain. Mainnet retargets every 2016 blocks. testnet4 follows BIP94. regtest never retargets.
7. Time must beat median time past of the last 11 headers, and must not pass now plus 2 hours. Future headers are held, not banned.
8. Version must meet the BIP34, BIP66, and BIP65 heights.
9. Work is 2^256 / (target + 1). Chain work adds parent work plus this work.
10. Low work branches stay in memory. They reach disk only near best tip work.

Best chain means most cumulative work, not most blocks. A new best tip emits a reorg event.

## Peers

Bootstrap uses manual peers if set, else DNS seeds and fixed seeds, then addr gossip. At most one outbound peer per netgroup. Target is 8 outbound peers, headers only. Mnara sends sendheaders, sets relay false, and advertises no services.

Rotation drops the stalest peer every 30 minutes. Silence dials 2 extra peers. Bad data raises a ban score. Bans last 24 hours.

## Quorum

A result counts as witnessed only when peers in at least 4 distinct netgroups confirm the tip. Fewer means Cannot witness.
