# LND

Mnara talks to LND over gRPC with TLS and a macaroon. It only reads. It refuses to start with a macaroon that can write.

## Calls

- GetInfo (info:read): block height, block hash, synced to chain, header time, network.
- ListChannels (offchain:read): channel IDs and points (milestone 2).
- ClosedChannels (offchain:read): known closes (milestone 2).
- PendingChannels (offchain:read): force closes in progress (milestone 2).

## Macaroon

Bake a narrow macaroon:

```
lncli bakemacaroon --root_key_id 8 --save_to ./mnara.macaroon info:read offchain:read
lncli printmacaroon --macaroon_file ./mnara.macaroon
lncli deletemacaroonid 8      # revoke later
```

`readonly.macaroon` also works. Never use `admin.macaroon`.

## Guard

At start, and in `mnara check-macaroon`, Mnara decodes the macaroon and refuses any permission whose action is not read. admin.macaroon is refused, with the write permissions named. There is no override flag.

The LND network must equal the configured network, or Mnara refuses to start.
