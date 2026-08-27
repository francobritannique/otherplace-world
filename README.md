# otherplace-world

Published world models and evidence for **OtherPlace**, served to installed
apps as a signed content channel.

This repository holds **artifacts only** — no engine, no application source,
and no key material. It is written to by the publishing tool in the main
repository and read by shipped apps.

## Layout

```
manifest.json          the index a client fetches first
manifest.json.sig      detached Ed25519 signature over manifest.json's exact bytes
worlds/<id>/<version>/world.json
feeds/feeds.json                      news feed roster
evidence/<date>/premise-checks.jsonl  verified premise verdicts with their quotes
resolutions/anchor-resolutions.jsonl  scored forecasts, append-only
```

## How a client decides to trust what it reads here

Every step refuses rather than degrades, and a refusal leaves the client on the
model it already had:

1. The signature over `manifest.json` verifies against a public key **pinned in
   the application binary**. An unsigned or badly-signed manifest is refused —
   there is no compatibility mode.
2. The manifest's `generated` timestamp is not older than the newest one already
   accepted, so a validly-signed *stale* manifest cannot roll a client back.
3. The artifact's SHA-256 matches what the manifest claims.
4. The artifact's own `contentHash` — the model's canonical identity, computed by
   the engine — matches what the manifest claims it is shipping.
5. The artifact compiles.

`contentHash` is FNV-1a: it identifies a model, and it is *not* a security
mechanism. Integrity is SHA-256 and authenticity is the signature.

## Line endings

`.gitattributes` declares every published artifact binary-exact. Do not relax
that: byte-for-byte is the whole verification story, and a normalising checkout
would surface as a tampering alarm.
