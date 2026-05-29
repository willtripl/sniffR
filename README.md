# sniffR - Tactical Network & NFC Recon

A tactical, multi-threaded network and NFC scanner built for Android.

> **⚠️ DISCLAIMER: Ethical Use Only**
> This tool was developed solely for authorized network auditing, educational purposes, and personal portfolio demonstration. Do not use sniffR on networks or hardware you do not own or have explicit permission to test.

## Overview
sniffR is a mobile passive reconnaissance tool designed to map local subnets and interact with physical NFC tags. Instead of relying on heavy third-party frameworks, it leverages native OS-level ICMP pings and raw TCP port knocking to identify active hosts, open services, and hidden consumer electronics on a local area network.


## Core Capabilities
* **Multi-Stage Subnet Sweeping:** Maps the local `/24` subnet by launching a parallel ICMP ping sweep followed by targeted TCP port knocks (Ports 80, 443, 22, 3389, 8080, 5353) to bypass basic stateful firewalls that drop unexpected TCP packets.
* **Hardware Interface Bypassing:** Uses `dart:io` `NetworkInterface` iteration to explicitly bind to active Wi-Fi hardware adapters, bypassing Android's default restricted Wi-Fi managers that fail when VPNs or cellular data are active.
* **Actionable Recon Exports:** Compiles the scanned network topology into a formatted JSON log and pushes it through the native Android share sheet for off-device analysis and red-team reporting.
* **NFC Interception:** Initializes hardware radios to read raw NDEF payloads from physical NFC tags.

## Technical Realities & OS Limitations
If you are wondering why this tool does not resolve vendor MAC addresses or perfect human-readable hostnames for every device, welcome to modern mobile OS security:

1. **The ARP Cache Lockdown:** As of Android 10, Google completely blocked third-party applications from accessing the local ARP cache. Without root access, grabbing physical MAC addresses to cross-reference with an OUI (Organizationally Unique Identifier) database is impossible. 
2. **mDNS UUID Obfuscation:** sniffR utilizes UDP/mDNS interception to catch local broadcast names. However, modern Apple hardware and smart home hubs now hash or randomize their broadcast UUIDs (e.g., `26d398e9...`) specifically to prevent passive network sniffers from tracking and fingerprinting them. 

When the OS blocks the hardware IDs, this tool pivots to behavioral fingerprinting via port analysis.

## Build & Deploy
Ensure you have the Flutter SDK installed and an Android device connected via ADB.

```bash
git clone [https://github.com/YOUR-USERNAME/sniffr.git](https://github.com/YOUR-USERNAME/sniffr.git)
cd sniffr
flutter clean
flutter pub get
flutter build apk --release
