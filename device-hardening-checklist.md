# IoT Device Hardening Checklist

A practical checklist I use when bringing a new IoT device onto the home network, based on what actually showed up as a risk in the threat model and packet analysis work elsewhere in this repo — not a generic copy-paste list.

## Before connecting the device
- [ ] Check if the device supports local-only control (no cloud dependency) — prefer it if it does
- [ ] Research whether the vendor has a history of disclosed vulnerabilities or breaches
- [ ] Set up the device on an isolated network (mobile hotspot or isolated VLAN) for initial pairing, not your main Wi-Fi, given that pairing phases sometimes expose credentials in cleartext (see `device-threat-model/`)

## Network configuration
- [ ] Place the device on a dedicated IoT VLAN, not the trusted network
- [ ] Confirm firewall rules block the device from initiating connections to trusted-network devices
- [ ] Restrict outbound traffic to only the ports/protocols the device actually needs (verify with a packet capture, don't just trust the manual)
- [ ] Disable UPnP on the router if not already — prevents devices from opening ports on the router without asking

## Device-level settings
- [ ] Change any default admin credentials immediately
- [ ] Disable local web/telnet admin interfaces if not needed day-to-day
- [ ] Check for and apply firmware updates before first real use
- [ ] Turn off features you're not using (remote access, voice assistant integration, etc.) — every enabled feature is attack surface

## Ongoing
- [ ] Periodically capture and review the device's traffic pattern (see `packet-analysis/` in the networking repo for how) — vendors change behavior via firmware updates without always disclosing it
- [ ] Check for firmware updates every few months, since most consumer IoT devices don't auto-update reliably
- [ ] Reassess whether the device still needs to be connected at all — not every "smart" device is worth the risk it carries

## The honest bottom line
Most of this checklist exists because consumer IoT vendors optimize for easy setup over secure defaults. None of these steps require trusting the vendor to do the right thing — that's the point.
