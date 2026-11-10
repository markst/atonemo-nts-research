# Evidence and provenance

## Confidence rules

- **Observed:** visible in a supplied photograph, saved typed setting, recorded request/response or analyzed capture. Name the artifact and scope.
- **Manufacturer-documented:** stated by a linked vendor source. This is not a measurement of the examined unit.
- **Inference:** a plausible explanation consistent with evidence; label it explicitly.
- **Unknown:** not demonstrated, or the original evidence cannot be recovered. A failed probe is not proof of impossibility.

This repository is a curated research record, not the original conversation transcript. Much of the middle transcript was unavailable when it was assembled. Retained files, runtime response objects and packet captures recovered substantial details; missing experiments have not been reconstructed from confident-sounding summaries.

## Included artifacts

| File | Derivation and limits |
|---|---|
| `data/settings-selected.json` | Explicit allowlist of 28 saved collector entries. Includes typed values and metadata, excludes broad network/account/certificate dumps. A collector entry is not necessarily a raw HTTP response. |
| `data/http-observations.json` | Six selected request/response pairs decoded with `tshark -2`, joined using response frame references. Hosts/IPs, authorization headers and challenge parameters excluded. Includes successful reads and rejected update actions. |
| `data/nts-mixtapes.json` | Five fields per station from the saved public NTS catalogue; 16 entries. Original retained response URL and self-link establish provenance. |
| `data/capture-summary.json` | Passive-capture statistics, starting baseline, DNS/SNI aggregate, limitations and private-source SHA-256 inventory. Not packet payloads. |
| `images/board-component-side.png` | Cropped/resized owner photograph; identifiers, QR codes and handwritten markings masked. |
| `images/board-underside.png` | Cropped/resized owner photograph showing numbered test pads. |

The PNGs were rebuilt from pixel data using only `IHDR`, `IDAT` and `IEND` chunks; no source metadata or original thumbnail is embedded. Original photographs are not included.

## Private sources

Raw originals remain outside this repository. The inventory names generic capture files and their hashes, not their owner's filesystem paths. Hashes let the owner match excerpts to retained originals without publishing payloads.

Key selected frame pairs:

| Private capture | Request → response | Observation |
|---|---|---|
| `device-boot.pcap` | 220658 → 220665 | Firmware version read, HTTP 200 |
| `radio-fwcheck.pcapng` | 189 → 196 | `uiStartUpdate` activation, HTTP 401 |
| `radio-fwcheck.pcapng` | 2536 → 2543 | `checkForUpdate` activation, HTTP 401 |
| `radio-fwcheck.pcapng` | 483 → 490 | Internet-update container, HTTP 200 |
| `radio-fwcheck.pcapng` | 11584 → 11590 | Action metadata read, HTTP 200 |
| `radio-fwcheck.pcapng` | 17858 → 17865 | Updater state `idle`, HTTP 200 |

Use the JSON's frame references as the canonical selected record. Captures are deliberately not included, so an outside reader cannot independently reassemble all packets from this repository alone. Reproduce on an authorized unit and contribute another sanitized observation rather than treating withheld raw evidence as a verified universal contract.

The starting baseline records firmware version and updater state; it is **not** a post-capture firmware-version check.

## Primary references

Reviewed during repository preparation on 2026-11-06:

- [Atonemo NTS Radio Player](https://atonemo.com/products/nts): physical interface, advertised services, output/power specification and automatic updates.
- [StreamUnlimited Stream210](https://www.streamunlimited.com/stream210/): module-family specifications and software capabilities; not this product's enabled configuration.
- [NTS Infinite Mixtapes API](https://www.nts.live/api/v2/mixtapes): catalogue and public stream references.

## Verification record

Assembly checks cover JSON parsing, allowlist selection, selected HTTP frame/body provenance, catalogue comparison, local Markdown links, image encoding/redaction review and Git ignore behavior. No new firmware writes, update activation, TLS interception or hardware probing was performed to create the repository.

The last recorded device address refused a read-only connection during preparation. Live API examples are therefore backed by historical packet exchanges, not a claim of current device reachability. Unavailable earlier experiment details remain explicitly unknown.
