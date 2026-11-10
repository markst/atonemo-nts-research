# Atonemo NTS Radio Player research

Independent hardware, firmware and local-protocol notes for the **Atonemo NTS Radio Player**. Not affiliated with Atonemo, NTS, StreamUnlimited or Realtek.

The goal is to understand a useful, deliberately simple device: two live-radio buttons, a mixtape selector and a volume knob. In particular: **can it retain that physical interface while letting its owner choose the stations?**

**Persistent station remapping, custom firmware and a working recovery procedure have not been demonstrated.** This repository is an evidence record and a starting point for further investigation, not an unlock tool.

Research: November 2026. One examined unit; do not generalize its firmware or board revision to every device.

## Findings at a glance

| Area | Finding | Evidence |
|---|---|---|
| Hardware | Stream210 module marked `Vers: 2A2`; PCB marked `V0.61_2026/04/22` | [Teardown](docs/hardware.md) |
| Firmware | `1.0.22.0xefb17f3`, board `AtonemoStream210L2`, Linux 6.6.30-based build | [Selected settings](data/settings-selected.json) |
| LAN API | Captured successful `/api/getData` and `/api/getRows` reads | [Protocol notes](docs/local-api.md) |
| Write authorization | Two update-activation requests received HTTP 401 and `HMAC_SHA256` challenges | [Wire excerpts](data/http-observations.json) |
| Radio streams | Public catalogue contains the 16 Infinite Mixtapes with direct and HLS URLs | [Stations](docs/stations.md) |
| Firmware traffic | 51 minutes 36 seconds of passive capture; no identifiable update download | [Capture analysis](docs/firmware-network.md) |
| USB | No successful radio enumeration in the recorded direct-connection snapshots; the VIA “keyboard” was not established as the radio | [USB correction](docs/hardware.md#usb-observations) |

## Start here

- [Hardware and redacted board photographs](docs/hardware.md)
- [Local API: requests, response shapes and authentication limits](docs/local-api.md)
- [Firmware, network capture and failed update activation](docs/firmware-network.md)
- [NTS stream catalogue and station-selection questions](docs/stations.md)
- [Evidence provenance and confidence rules](docs/evidence.md)
- [Prioritized investigation plan](docs/investigation.md)
- [Contributing safely](CONTRIBUTING.md)

The `data/` directory contains selected, sanitized observations. There is no application to build and no dependency installation is required to read the findings. Reproduction commands use existing `curl`, Python 3 and, for packet analysis, Wireshark's `tshark`/`capinfos` tools.

## Safety and publication

Only investigate equipment and networks you own or have permission to inspect. Do not expose the device's local API to the Internet. Read-only examples are separated from historical write attempts; **do not replay update actions as probes**.

Raw settings, packet captures, original photographs, diagnostic archives, certificates and keys are not included. The photographs here have identifiers/QR codes masked and are encoded without metadata. `.gitignore` reduces accidental inclusion but is not a secret scanner.

No firmware image, audio recording, vendor app binary or authentication key is distributed. Names and third-party materials remain their owners' property. **No redistribution license has been selected for the original repository material yet**; public visibility alone is not a license. Agree a license before inviting reuse beyond ordinary repository viewing/contribution proposals.
