# MQTT Security — Lab Notes

Set up a Mosquitto broker on a Raspberry Pi to test MQTT security configurations, since a lot of consumer IoT gear uses MQTT under the hood and I wanted to actually see what's exposed when it's misconfigured versus locked down.

## Setup
- Mosquitto broker on Raspberry Pi 4, isolated on its own VLAN
- Test publisher/subscriber clients using `mosquitto_pub`/`mosquitto_sub`
- MQTT Explorer for a GUI view of topics and messages
- Wireshark for packet-level inspection

## Test 1: Default config — no TLS, no auth

Started the broker with default settings (anonymous access allowed, port 1883, no encryption).

**What I found:**
- Anyone on the network could connect and subscribe to `#` (the wildcard topic) and see every message published by any device, with zero credentials
- Publishing was also unauthenticated — I could publish to any topic, including ones a real device would be publishing sensor data or listening for commands on
- Wireshark capture showed the entire MQTT payload in cleartext, including topic names and message content, since there's no TLS on port 1883

**What this means practically:** if a real smart-home setup runs MQTT this way on a shared network, anyone else on that network (a guest, a compromised IoT device, anyone who gets on your Wi-Fi) can read every sensor value in your house and, worse, publish fake commands to control devices.

## Test 2: TLS + username/password auth enabled

Reconfigured the broker: enabled TLS on port 8883 with a self-signed cert for lab purposes, required username/password auth, disabled anonymous access.

**What I found:**
- Wireshark capture of the same traffic now showed only encrypted TLS handshake and application data — no readable topic names or payloads
- Connection attempts without credentials were rejected at the broker level, logged in Mosquitto's log file
- Client setup got meaningfully more annoying (cert distribution, credential management) — this is very likely why so many consumer devices ship without it enabled by default. Security here has a real usability cost that vendors seem to consistently choose not to pay.

## Test 3: ACLs — restricting which topics a client can use

Beyond auth, I set up per-user ACLs so a given device credential could only publish to its own topic namespace (e.g. `sensors/livingroom/#`) rather than being able to publish or subscribe anywhere once authenticated.

**Why this matters:** authentication alone answers "who are you," not "what should you be allowed to do." Without ACLs, one compromised device's credentials could be used to spoof messages from every other device on the broker.

## Takeaways
1. Anonymous, unencrypted MQTT (the out-of-box state for a lot of tutorials and some consumer bridges) is close to as bad as it sounds — full read/write access to everything with no credentials at all.
2. TLS + auth closes most of the obvious hole, but ACLs are the part that's easy to skip and actually matters for limiting blast radius if one device does get compromised.
3. This directly informed the IoT VLAN isolation approach in `network-engineering-labs/` — even a properly configured MQTT broker is still safer behind network segmentation than exposed to a flat network, since defense in depth beats trusting any single layer to be configured perfectly forever.
