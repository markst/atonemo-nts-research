# Firmware and network investigation

## Recorded firmware

| Field | Observed value |
|---|---|
| Release | `Streamplayer-1.0.22` |
| Firmware version | `1.0.22.0xefb17f3` |
| SDK build string | `Streamplayer-1.0.22-0-gefb17f3` |
| BSP | `25.03-Nickel-6-gc45105267` |
| Machine | `streamplayer` |
| Kernel | `6.6.30-00246-g261db1487919-dirty` |
| Kernel build | 2026-04-09, ARM GNU EABI toolchain, GCC 13.3.0, SMP PREEMPT |

These are device-reported strings, not independently verified source revisions or reproducible-build results. See [selected settings](../data/settings-selected.json).

## Updater configuration is not scheduling proof

| Node under `settings:/fwupdate/` | Recorded value | Qualification |
|---|---|---|
| `periodicCheckInterval` | `6` | Title explicitly says hours |
| `minSystemIdleTime` | `600` | Unit not explicit in the saved title; marked modifiable |
| `retryCount` | `3` | Configured download retry count |
| `stopOnFirstUpdateFound` | `false` | Configuration only |

The baseline updater state was `idle`.

The generic UI text at `ui:/settings/update/usbUpdateInfo` says: “Plug in USB stick and reboot while pressing the update button.” **This is not a verified recovery procedure for the NTS hardware.** No update-button mapping, image filename, filesystem requirement or successful USB update is known here.

Atonemo advertises automatic OTA updates on its [product page](https://atonemo.com/products/nts). The local attempts to activate `firmwareupdate:uiStartUpdate` and `firmwareupdate:checkForUpdate` were rejected with HTTP 401; see [API evidence](local-api.md#authentication-and-failed-writes). The attempted checks cannot be treated as successfully triggered updates.

## Completed passive capture, 2026-11-05

Capture ran on a macOS Internet Sharing bridge. The ring was configured for 12 files of 100000 kB each; two files were retained. The baseline says this capture run did **not** enable MITM interception.

| Segment | Packets | File bytes | Duration, seconds |
|---|---:|---:|---:|
| 1 | 140289 | 100000008 | 2733.972713 |
| 2 | 19055 | 13597052 | 362.190467 |
| Total | 159344 | 113597060 | 3096.163181 |

Total duration is **51 minutes 36 seconds**, not six hours. A one-microsecond boundary gap accounts for the rounded total. File sizes include packet-capture metadata; they are not firmware or application payload sizes.

Both the capture process and its idle-sleep guard exited with code 0. The result was analyzed after completion, not inferred from process exit.

The host observations combined outbound DNS questions and TLS ClientHello SNI appearances:

| Host | Combined appearances |
|---|---:|
| `time.google.com` | 2 |
| `connectivitycheck.gstatic.com` | 156 |
| `stream-relay-geo.ntslive.net` | 11 |
| `streams.radiomast.io` | 35 |
| `audio-edge-d34v9.syd.o.radiomast.io` | 30 |
| `stream-mixtape-geo.ntslive.net` | 71 |

These are **not counts of connections, downloads or update checks**. DNS responses mapped the dominant data-transfer destination to the Radiomast audio edge. Conversation summaries showed approximately 90 MB inbound in segment 1 and 12 MB in segment 2 from that endpoint.

**Result:** no identifiable firmware-update hostname or separate firmware download was found. No decoded plaintext HTTP request originating from the radio matched the analysis filter.

**Limits:** TLS payloads were not decrypted; an update check could be hidden in encrypted traffic or use an already-known host. The window was shorter than the configured six-hour interval. Absence of an identifiable download does not establish absence of a check, absence of updates, or permanent lack of a server. The outgoing-request filter says nothing about plaintext requests from a controller to the radio's LAN API.

Full numeric summary and SHA-256 digests of the private captures: [capture-summary.json](../data/capture-summary.json).

## Reproduce passive observation

Use an authorized observation point through which the radio's traffic actually passes. An arbitrary Wi-Fi interface is not automatically such a point. Determine the interface and the radio's current MAC locally; never publish them with raw output.

With Wireshark command-line tools already installed, the following shell recipe captures only after you set the variables. It does not alter routing, DNS, firewall rules, device configuration or certificate trust:

```sh
: "${CAPTURE_INTERFACE:?Set the capture interface}"
: "${DEVICE_MAC:?Set the device MAC locally}"
umask 077
mkdir -p private/captures
# Stop with Ctrl-C. Ring files will overwrite the oldest segment when full.
dumpcap -i "$CAPTURE_INTERFACE"   -f "ether host $DEVICE_MAC or (udp and (port 67 or port 68))"   -b filesize:100000 -b files:12 -w private/captures/radio.pcapng
```

The DHCP clause can capture identifiers belonging to **other devices**. Keep the directory private. Capture permission requirements depend on the host; this repository does not install tools or change privilege rules. Preserve the ring before wraparound erases evidence, and keep the host awake through your normal power settings if needed.

Inspect an individual saved segment without contacting the radio:

```sh
: "${CAPTURE_FILE:?Set one captured segment path}"
capinfos -T -c -s -a -e -u "$CAPTURE_FILE"
tshark -r "$CAPTURE_FILE"   -Y "eth.src == $DEVICE_MAC && ((dns.flags.response == 0 && !mdns) || tls.handshake.type == 1 || http.request)"   -T fields -e dns.qry.name -e tls.handshake.extensions_server_name   -e http.request.full_uri
```

Do not post that output without reviewing URLs and hostnames. Analyze all segments, not just the most recent file. TLS SNI may be absent or encrypted; a capture with no readable name is not necessarily a capture with no connection.

## Earlier interception and unresolved recovery

A separate `radio-mitm.pcapng`, mitmproxy preparation material and an encrypted diagnostic archive were retained privately. Their existence does **not** establish successful HTTPS decryption or certificate pinning. No reproducible interception outcome or updater URL survives in the recovered evidence.

Still unknown: manifest/download location, firmware package format, partition layout, installed trust roots, image-signature enforcement, secure-boot configuration, rollback protection, UART login and working backup/restore. Do not flash a guessed image or treat a diagnostic log archive as recovery media.
