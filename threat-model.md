# Threat Model: Smart Plug + App + Cloud (STRIDE)

**System:** Consumer smart plug, controlled via mobile app, connected through a vendor cloud API.

| STRIDE Category | Threat | Layer | Likelihood | Notes |
|---|---|---|---|---|
| Spoofing | Rogue device impersonates plug on local network | Device/Network | Medium | No mutual TLS between device and hub observed |
| Tampering | Firmware modified via unsigned OTA update | Device | Low | Vendor uses signed updates (verified via traffic capture) |
| Repudiation | No local logging of on/off events | Device | High | Only cloud-side logs exist; if cloud is unavailable, no local audit trail |
| Information Disclosure | Wi-Fi credentials sent during setup without encryption in early pairing phase | Device/Network | Medium | Observed cleartext SSID handshake before secure channel established |
| Denial of Service | Device becomes unresponsive if cloud API is unreachable | Cloud | High | No local-only fallback control; classic cloud-dependency issue |
| Elevation of Privilege | Mobile app stores auth token in plaintext local storage | App | Medium | Found via basic app data inspection, not decompilation |

## Highest-priority findings
1. **Cloud dependency for basic function** — device is unusable for even local on/off control if internet drops. Not just inconvenient, it's a DoS risk model vendors rarely disclose.
2. **Cleartext pairing phase** — a brief window during setup where credentials are exposed on the local network.

## Recommended mitigations
- Segment IoT devices onto their own VLAN so a compromised device can't reach trusted network resources (see `network-engineering-labs/vlan-segmentation-design`)
- Avoid setting up new devices on shared/public Wi-Fi given the pairing-phase exposure
- Treat "smart" home devices as always-on network participants requiring the same scrutiny as any other endpoint

## Methodology
Analysis based on network traffic capture (Wireshark) during setup and normal operation, plus basic inspection of app local storage. No firmware extraction or hardware-level testing performed.
