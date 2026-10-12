# Annalist ledger

Annalist ([annalist.dev](https://annalist.dev)) keeps an append-only archive of crowd-state data for AI agents.
This public repository is its **ledger**: once a day it records a fingerprint of everything the archive wrote the
day before, signed and timestamped in Bitcoin. It lets anyone check that a record existed on a given date, so the
history cannot have been backfilled or quietly edited later.

## What is in it

- `ledger/YYYY/MM/YYYY-MM-DD.json`: one entry per UTC day.
  - `entry.manifests`: every run manifest written that day, each with the sha256 of the manifest file.
  - `entry.root`: sha256 of the manifest hashes joined by newlines, in the listed order.
  - `signature`: Ed25519 over the canonical JSON of `entry` (keys sorted, no spaces, UTF-8).
- `ledger/YYYY/MM/YYYY-MM-DD.json.ots`: an [OpenTimestamps](https://opentimestamps.org) proof that the entry file
  existed at that time. Proofs start as pending and are upgraded to full Bitcoin attestations within a day or two.
- `keys/<key_id>.json`: public signing keys. The current key is `187e49f200625a2f`.

Each run manifest in turn lists every archive file written by that run with its sha256 and size, and is signed
with the same key. Data responses served by the Annalist API carry the same kind of signature.

## Verifying

1. Check the timestamp: `ots verify ledger/2026/10/2026-10-13.json.ots` (needs the OpenTimestamps client and a
   Bitcoin block source, or use opentimestamps.org).
2. Check the signature: verify `signature` against the canonical JSON of `entry` with the public key in `keys/`.
3. Given a manifest from the archive, hash its file bytes with sha256 and find that hash in the day's entry.

## Contact

Open an issue on this repository.
