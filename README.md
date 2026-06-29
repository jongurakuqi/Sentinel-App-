# Sentinel

**Private messaging that answers to no one.** End-to-end encrypted, no phone number,
no ads, no central server you can't replace. Sentinel is the communication half of the
**Stellar & Sentinel** ecosystem — "X Chat done honestly": real E2EE, a swappable
decentralized backend, and a radically honest threat model.

- **Website:** https://website-three-wheat-36.vercel.app
- **Principles:** no invented cryptography · no blockchain · messaging only · honest about limits

What Hercules Encryption means (the novel angle)
Instead of another generic “end-to-end encrypted” line, the messaging is positioned as:

Bitcoin-curve identity — secp256k1, same math as Bitcoin wallets (npub / nsec), not a phone number
NIP-44 sealing — ChaCha20-Poly1305 on every DM before it hits a relay
Decentralized transport — public Nostr relays, no central inbox
Portable keys — your identity works across the Nostr ecosystem
Pitch line: “Not phone-number encryption. The same secp256k1 curve that secures Bitcoin — applied to 1:1 messaging.”








> **Sentinel is an early prototype and has not been independently security-audited.**
> For high-risk use prefer [Briar](https://briarproject.org) or [Signal](https://signal.org).

---

## What's in this repository

This repo holds the Sentinel project in three parts:

| Path | What it is | Stack | Status |
|------|------------|-------|--------|
| [`sentinel/`](sentinel/) | **The flagship messenger** — full E2EE 1:1/group chat, media, disappearing messages, Nostr public channels, self-hostable/`.onion` relay | Expo (React Native) + TypeScript, Node WebSocket relay | Primary, runs on Android + Web |
| `src/` + `server/` | **Proximity prototype** — offline Bluetooth mesh + Nostr geohash channels | Vite + React + Capacitor | Experimental |
| [`website/`](website/) | **Marketing site** (this is what's deployed to Vercel) | Static HTML/CSS/JS | Live |

The two app implementations explore the same vision from two angles: `sentinel/` is the
complete, account-based encrypted messenger; the root prototype explores fully
serverless proximity (BLE mesh) messaging. See each folder's README for depth.

---

## Quick start

### The flagship messenger (`sentinel/`)

```bash
cd sentinel
npm install
npm run relay:install

# Terminal 1 — the relay (transport + identity directory; ciphertext only)
npm run relay            # ws://localhost:8787

# Terminal 2 — the app
npm run web              # or: npm start, then press a/w, or scan in Expo Go
```

Full docs, security model, deployment, and the Tor onion setup are in
[`sentinel/README.md`](sentinel/README.md).

### The proximity prototype (root)

```bash
npm install
npm run dev              # http://localhost:5173 — two tabs simulate BLE mesh peers
```

See [`ARCHITECTURE.md`](ARCHITECTURE.md) and [`MOBILE.md`](MOBILE.md).

### The website (`website/`)

```bash
cd website
# any static server works, e.g.:
npx serve .
```

Deploy/redeploy notes in [`website/README.md`](website/README.md).

---

## Architecture at a glance

Sentinel deliberately splits into surfaces with **different trust properties**:

| Surface | Transport | Encryption | Who can read |
|---------|-----------|------------|--------------|
| 1:1 & group chats | Sentinel relay (self-hostable / `.onion`) | **E2EE** (NaCl: Curve25519 + XSalsa20-Poly1305) | room members only |
| Public channels | Nostr public relays | none (public by design) | anyone |
| Proximity / offline | Bluetooth mesh (native) | per-link | nearby peers |

Your private keys are generated on-device and never leave it. The relay only ever sees
**ciphertext** and can be run by anyone — even as a Tor v3 onion service, so there's no
central point to block or seize.

---

## Security model (the honest version)

**What Sentinel guarantees**
- End-to-end encryption with audited NaCl primitives; the relay never sees plaintext or keys.
- Private keys never leave the device; readable history lives on-device, not in the cloud.
- No phone number, no ad tracking, no analytics. Open source and self-hostable.

**Honest limitations**
- Early prototype — not independently audited.
- Trust-on-first-use for public keys (key verification is on the roadmap).
- No forward secrecy yet (no Double Ratchet / Megolm).
- The relay can see metadata (who talks to whom, and when).
- Public (Nostr) channels are public by design — not encrypted.

Full detail: [`sentinel/README.md` → Security model](sentinel/README.md#security-model).

---

## Roadmap

- Offline Bluetooth mesh in the flagship app (native dev client).
- Embedded Tor (Arti) so `.onion` works without Orbot.
- Key verification (QR / safety numbers) to remove trust-on-first-use.
- Forward secrecy (Double Ratchet) and encrypted multi-device history.
- iOS App Store + Google Play distribution.

No payments, no social feed, no AI — Sentinel is messaging only, by design.

---

## Publishing & deployment

- **Push this repo to GitHub:** see [`PUBLISHING.md`](PUBLISHING.md).
- **Website is deployed on Vercel:** https://website-three-wheat-36.vercel.app
- **Relay hosting / Tor onion:** [`sentinel/server/onion/`](sentinel/server/onion/README.md).

---

## License

Released under the [MIT License](LICENSE). You're free to fork, self-host, and modify.
If you'd prefer a copyleft posture like Signal (AGPL-3.0) or Briar (GPL-3.0) to keep
downstream forks open, swap the `LICENSE` file accordingly.
