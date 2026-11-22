# Replaced APIs

Mnara uses no hosted API. Each need below is met from source.

| Need | Usual answer | Mnara answer |
|---|---|---|
| Current chain tip | Explorer API | P2P headers from diverse peers, verified in-house. |
| Is my node forked? | forkmonitor.info | In-house header tree, compared with LND. |
| Is this transaction confirmed in the best chain? | Explorer tx page | Fetch the block by height from peers. Recompute the Merkle root. Match the txid (milestone 2). |
| Was this output spent? | Explorer outspend page | BIP158 filters with cross-peer agreement, then the block (milestone 2). |
| Peer discovery | DNS only | DNS seeds and fixed seeds from open lists. `peers.connect` skips DNS. |
| Accurate time | NTP | The host clock. Mnara warns, never fetches time. |
| Notifications | Telegram, email services | Owner webhook or local command. |

If someone later wants push to a phone, the contract is a self-hosted receiver. It accepts `POST {network, status, node_height, network_height, since}` over HTTPS with a shared secret header. Mnara will not ship a hosted one.
