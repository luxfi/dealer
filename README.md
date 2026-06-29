# luxfi/dealer — ARCHIVED (not used)

> **Status: ARCHIVED / superseded. Do not use in production. Do not depend on this module.**

This repository preserves the **legacy trusted-dealer / reconstruct-at-combiner**
threshold-signing path that was removed from [`luxfi/pulsar`](https://github.com/luxfi/pulsar).
It exists only as a historical reference. It is **not** maintained, **not** built
or tested in CI, and **not** wired into any chain.

## What this was

A GF(q) seed-share committee threshold signer plus a trusted-dealer keygen:

| File | What it was |
|---|---|
| `legacy/large_dkg.go`, `large_threshold.go` | `LargeCombine` — **reconstructed the master seed at the combiner** at sign time (the "H-1 footgun": one node briefly held the full `sk`). |
| `legacy/large_reshare.go`, `large_types.go`, `largeshamir.go` | the wide-committee GF(q) seed-share + reshare machinery. |
| `legacy/bootstrap_dealer_test.go` | `DealAlgShares` — a **trusted dealer** that expanded the seed once and Shamir-shared `s1`. |
| `legacy/*_test.go` | the tests for the above. |

In `luxfi/pulsar` these files carried the `//go:build legacy_trusted_dealer`
tag — quarantined out of the production build — before being removed entirely.

## Why it is dead

`luxfi/pulsar` replaced this with a genuinely **dealerless** path, so the
trusted dealer and the reconstruct-at-combiner are gone at **both** ends:

- **Keygen** — dealerless **Mithril** short replicated secret sharing
  (`mithril_rss.go`), group key verifiable under stock FIPS-204 `mldsa65.Verify`.
- **Signing** — **no-reconstruct** BCC/CSCP + the Mithril hyperball 3-round
  signer; the secret is never reassembled, not even transiently at a coordinator.

The shared dealerless DKG primitives live in [`luxfi/dkg`](https://github.com/luxfi/dkg);
the shared Module-LWE primitives in [`luxfi/mlwe`](https://github.com/luxfi/mlwe).

## Build note

These files are `package pulsar` internals (they reference pulsar-internal
types). They are a **snapshot, not a standalone buildable module** — for the
buildable historical context see `luxfi/pulsar` git history at tag `v0.6.4`
(the last commit before the rip). This archive is for reading, not running.
