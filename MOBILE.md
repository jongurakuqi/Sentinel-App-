# Sentinel on iOS & Android

Sentinel ships as a Capacitor app with **real Bluetooth Low Energy** on phones. The web build still uses a local WebSocket relay to simulate mesh peers.

## Prerequisites

- Node 20+
- **iOS**: Xcode 15+, Apple Developer account (for device testing)
- **Android**: Android Studio, JDK 17

## Build & run

```bash
cd ~/Projects/bitchat-vibe
npm install
npm run cap:ios      # or: npm run cap:android
```

`cap:ios` / `cap:android` runs a production web build, syncs native projects, and opens Xcode or Android Studio.

From Xcode: select your physical iPhone → Run.  
From Android Studio: select a physical device → Run.

**Use two phones** side by side to test BLE peer discovery and `#mesh`.

## Bluetooth permissions

iOS `Info.plist` includes Bluetooth usage strings and background modes (`bluetooth-central`, `bluetooth-peripheral`). Android permissions are injected by the Capgo BLE plugin — grant Bluetooth (and location on older Android) when prompted.

## How phone BLE works

Phones alternate **advertise** and **scan** phases (duty cycling) because BLE central and peripheral modes are exclusive on most stacks.

| Phase | Role | What happens |
|-------|------|----------------|
| Advertise | Peripheral | Broadcasts callsign + short messages in the BLE device name |
| Scan | Central | Discovers nearby Sentinel peers and receives their broadcasts |

Room messages and DMs are encoded into compact advertisement names (~29 bytes). Keep messages short for reliable delivery; longer text may truncate.

Peer discovery shows up under **In range** on the Nearby tab. `#mesh` uses the radio; `#near-geo` still uses Nostr over the internet.

## Web vs native

| Platform | Mesh transport |
|----------|----------------|
| Browser (`npm run dev`) | WebSocket relay on port 3847 |
| iOS / Android app | Real BLE advertisements |

The UI shows **BLE radio** on native and **Mesh relay** in the browser.

## Troubleshooting

- **No peers**: Bluetooth must be on; keep both apps in the foreground initially.
- **iOS build fails**: Open `ios/App/App.xcodeproj`, set your Team under Signing & Capabilities.
- **Android scan empty**: Enable location services (required for BLE scan on many devices).
- **Changes not appearing**: Run `npm run cap:sync` after editing web code.

## Dev loop

```bash
npm run build && npx cap sync
# then rebuild in Xcode / Android Studio
```

For faster web UI iteration, use `npm run dev` in the browser; switch to native builds when testing radio.
