# Further investigation

The target is **owner-selected stations using the existing physical controls**, without needing a phone for everyday playback. A stream URL list, a successful one-off playback command or a shell prompt alone does not meet that target.

## 1. Establish a reproducible read-only baseline

Record firmware version, board revision, network topology and which read-only API queries succeed on another authorized unit. Compare the selected node values with this repository. Keep raw identifiers private.

**Evidence needed:** dated sanitized requests/responses, status codes and observed firmware version. Distinguish a reachable web page from a working control operation. Do not enumerate broad settings into a public issue.

## 2. Observe normal physical controls

With the radio operating normally, record one button press or wheel movement at a time and correlate it with the now-playing node, stream destination and any official app events. Measure wraparound and selection order rather than assuming catalogue order.

**Evidence needed:** control action → resulting channel/title/URL, before/after state, repeatability and behavior after power cycling. Avoid rapid switching or altering update/security settings.

## 3. Understand official authorization before attempting writes

Observe authorized app use on owned equipment, with account/network data kept private. Determine how it discovers the radio, whether BLE is only for setup, how the `HMAC_SHA256` challenge is answered, and which operations are permitted. Request documentation or an owner-supported API from Atonemo/StreamUnlimited.

**Evidence needed:** sanitized protocol structure, authorization boundaries and successful owner-authorized behavior. Do not commit shared secrets, signing keys, challenge tokens, paired-home data, credentials or app binaries. Do not assume the presence of an HMAC challenge identifies its key derivation.

## 4. Locate the station mapping

Determine whether the list lives in settings, an application resource, a downloaded catalogue or firmware code. The empty debug URL and Spotify presets are not evidence of that mapping. Prefer an owner-supported configuration mechanism over modifying binaries.

**Evidence needed:** a backed-up original value, an understood authorized operation, an observed effect on the physical control and a safe restoration method. Only then test an owner-selected station, persistence over reboot and power loss, and behavior after restoring the original mapping.

## 5. Capture an actual update check

The existing passive window was shorter than the six-hour configured interval. The manual activation attempts were rejected. A better observation is a longer capture covering idle time, or a documented official-app check if that UI exists; neither should be described as guaranteed to trigger a download.

**Evidence needed:** confirmed trigger/state transition, correlated network connections, an identifiable manifest or package reference, and before/after version data. Archive ring segments before overwrite. Keep traffic passive first; TLS interception can change device behavior and requires a separate controlled experiment.

If an update package becomes legitimately available, analyze it offline before any flash attempt. Do not redistribute proprietary payloads without permission. Document package checksums/format, partition targets, signature/rollback checks and the recovery path; don't infer “unsigned” or “locked” from a failure.

## 6. Map hardware access conservatively

Seek schematics/pin documentation, or identify ground and pads electrically before connecting probes. Module specifications advertise UART/USB capabilities but do not identify this board's test pads.

**Evidence needed:** annotated board revision, continuity measurements, logic voltage, signal direction and receive-only boot output. A console does not imply root access, a writable bootloader or safe recovery. Preserve the working unit; no blind pad-short, voltage injection or guessed firmware flashing.

## Useful contribution outcomes

- Reproduction or correction of a particular recorded finding on a stated revision.
- A measured control-to-station map.
- Public vendor documentation for authentication, configurable stations or recovery.
- A privacy-safe capture excerpt proving a real update transaction.
- A measured test-pad map, with uncertainty and voltage documented.

Record negative results with exact scope and expected behavior. See [CONTRIBUTING](../CONTRIBUTING.md).
