# Whitepaper map

Bitcoin: A Peer-to-Peer Electronic Cash System, https://bitcoin.org/bitcoin.pdf. Each row says what Mnara code verifies or computes.

| Section | Name | Where in Mnara | What the code does |
|---|---|---|---|
| 1 | Introduction | README | Replace trust in an explorer with proof of work. |
| 2 | Transactions | anchor (milestone 2) | Parse the funding transaction. Compute its txid. Match the outpoint. Later, spot the spend. No signature checks. |
| 3 | Timestamp Server | consensus | Each header commits to the prev hash. Median time past and the 2-hour future limit. |
| 4 | Proof-of-Work | consensus, chain | Double SHA-256. Compact nBits to target. Hash at most target. Retarget. Chain work. Most-work rule. |
| 5 | Network | p2p, chain | Real P2P handshake and header sync. Keep competing branches. Switch on more work. Reorg events. |
| 6 | Incentive | docs only | Not used in code. Honest majority work is why the most-work rule is safe to rely on. |
| 7 | Reclaiming Disk Space | merkle, store | Merkle root recompute. Branch storage instead of blocks. 80-byte headers. About 4.2 MB per year. |
| 8 | Simplified Payment Verification | the whole product | Keep headers of the most-work chain. Link a transaction to its place in the chain (milestone 2). Alert your own node. |
| 9 | Combining and Splitting Value | none | Not used. |
| 10 | Privacy | p2p, filters | Never ask a peer about an address, script, or outpoint. Fetch whole filters and blocks. relay is false. |
| 11 | Calculations | stats, witness | Port of the paper function. Tested against the paper tables. Poisson silence sets the silence alarm. |
| 12 | Conclusion | docs | Motivation only. |

Suggested reading order: 3 and 4 first, then 11, then 5, then 8, then 7 and 2.
