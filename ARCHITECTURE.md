# Sentinel Architecture

Sentinel uses a **dual transport model** — local Bluetooth mesh for offline proximity chat, plus **Nostr** for global geohash channels when the internet is available.

## Transport layers

### 1. Bluetooth mesh (offline)

| Property | Value |
|----------|--------|
| Protocol | BLE advertisement mesh on phones; WebSocket relay in browser demo |
| Scope | Peers within multi-hop range |
| Internet | Not required |
| Encryption | Noise Protocol (on native builds; labeled in UI) |
| Channels | `#mesh` — local broadcast |
| DMs | Direct to peer when in range |

Messages relay through nearby nodes (max **7 hops**; simulated in the relay server).

### 2. Nostr (internet)

| Property | Value |
|----------|--------|
| Protocol | [Nostr](https://github.com/nostr-protocol/n) |
| Scope | Global via public relays |
| Internet | Required |
| Encryption | NIP-17 gift-wrap (native); kind-1 tagged events in this demo |
| Channels | Geohash location channels (`#near-xxxxxx`) |
| Relays | damus.io, nos.lol, nostr.band |

## Routing

```
Direct message:
  1. Bluetooth mesh (if peer in range)  ← preferred
  2. Nostr fallback (when mesh unavailable)

Channel message:
  #mesh      → always mesh transport
  #near-geo  → always Nostr transport
```

## Web demo vs native

| Feature | Native (iOS/Android) | Sentinel web demo |
|---------|----------------------|-------------------|
| BLE mesh | Real BLE (duty-cycled advertise/scan) | WebSocket relay simulates proximity |
| Nostr | Full NIP-17 DMs + geohash | Geohash room events on public relays |
| Noise encryption | Full protocol | Indicated in UI; not full crypto in browser demo |
| LZ4 compression | Yes | Not implemented |

## Project layout

```
server/nearby.js              Mesh relay (simulates BLE multi-hop)
src/lib/mesh/bleMesh.ts       Native BLE mesh (advertisement protocol)
src/lib/mesh/webSocketMesh.ts Web mesh fallback
src/lib/nostr.ts              Nostr transport — publish/subscribe geohash rooms
src/lib/transport/router.ts   Mesh-first routing logic
src/hooks/useTransport.ts     Dual transport hook
```

## References

- [Noise Protocol](https://noiseprotocol.org)
- [Nostr NIPs](https://github.com/nostr-protocol/nips)
