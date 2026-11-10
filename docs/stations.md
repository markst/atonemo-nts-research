# NTS streams and physical station selection

## Public catalogue

NTS exposes the Infinite Mixtape catalogue at:

**https://www.nts.live/api/v2/mixtapes**

The retained HTTP response object and the catalogue's `links` identify this source. The endpoint was read again during repository preparation. [nts-mixtapes.json](../data/nts-mixtapes.json) preserves only each station's title, alias, direct stream URL and AAC/MP3 HLS playlist URLs; artwork and audio are not copied.

The saved 16-station list matches the titles listed on [Atonemo's NTS product page](https://atonemo.com/products/nts). In catalogue order:

| Title | NTS direct-stream path |
|---|---|
| Poolside | `mixtape4` |
| Slow Focus | `mixtape` |
| Low Key | `mixtape2` |
| Memory Lane | `mixtape6` |
| 4 To The Floor | `mixtape5` |
| Island Time | `mixtape21` |
| The Tube | `mixtape26` |
| Sheet Music | `mixtape35` |
| Feelings | `mixtape27` |
| Expansions | `mixtape3` |
| Rap House | `mixtape22` |
| Labyrinth | `mixtape31` |
| Sweat | `mixtape24` |
| Otaku | `mixtape36` |
| The Pit | `mixtape34` |
| Field Recordings | `mixtape23` |

Direct paths use `https://stream-mixtape-geo.ntslive.net/` as their base. Preserve upstream aliases rather than deriving them from display titles: **Low Key** currently uses `100-percent-hip-hop`.

These are public service references, not device secrets. HLS UUIDs identify public streams, not this radio. URLs and titles may change. API retrieval is not a playback/codec test of every stream.

## Fetch a fresh reference list

This reads the public catalogue; it does not control the radio or download audio:

```sh
curl --fail --silent --show-error --max-time 20   'https://www.nts.live/api/v2/mixtapes' |
  python3 -c 'import json,sys; data=json.load(sys.stdin); fields=("title","mixtape_alias","audio_stream_endpoint","audio_stream_endpoint_hls_aac","audio_stream_endpoint_hls_mp3"); print(json.dumps([{k: row[k] for k in fields} for row in data["results"]], indent=2))'
```

The command outputs a bare station list; the repository JSON wraps the saved list with source/provenance notes. Review a fresh result rather than silently replacing the historical snapshot. Do not redistribute audio or assume the public API constitutes a service guarantee.

## What this does not establish

Atonemo documents two buttons for NTS live channels and a wheel for the 16 Infinite Mixtapes. The public catalogue order is **not a measured mapping of wheel positions or firmware indices**. Exact live-channel URL paths are not asserted by this snapshot.

The saved `ui:/debug/testPlayback/url` and Spotify preset settings are leads, not evidence of configurable physical controls. No retained result demonstrates:

1. Playback of an arbitrary owner-selected URL through the local API.
2. Binding that URL to an NTS button or wheel selection.
3. Persistence of that binding after reboot, power loss or an OTA update.
4. A supported restore path after an incorrect mapping.

Do not collapse those into a single claim that “custom stations work.” The investigation goal is the complete physical-control behavior, not merely sending a stream from a phone.
