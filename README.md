# IoT Security Labs

Threat modeling and protocol-level analysis of IoT devices, using consumer smart-home gear as test subjects in a home lab.

## Why I built this
IoT sits at the intersection of a lot of the domains I work in — it's networking (devices talking constantly), it's cybersecurity (weak defaults, unpatched firmware), and increasingly it's GRC (device inventories, vendor risk). I wanted projects that show I understand IoT risk from more than one angle.

## Projects

### `device-threat-model/`
A STRIDE-based threat model for a smart plug + companion app + cloud backend setup. Covers attack surface (device, mobile app, cloud API, local network) and realistic threats for each layer, not just "hackers could take over your lightbulb."

### `mqtt-protocol-notes/`
Notes and a lab writeup on MQTT security — testing an MQTT broker with and without TLS/auth enabled, and what's actually visible to someone sniffing the network when it's misconfigured.

### `device-hardening-checklist.md`
A practical checklist for hardening consumer IoT devices on a home or small-office network (segmentation, firmware updates, disabling UPnP, etc.)

## Tools used
Wireshark, MQTT Explorer, a Raspberry Pi running Mosquitto as a test broker, nmap for device fingerprinting

## Notes
All testing was done against my own lab devices on an isolated VLAN, not against any third-party or production systems.
