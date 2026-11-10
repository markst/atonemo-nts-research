# Local HTTP API

## What was actually observed

The captures contain local HTTP requests to these routes:

| Route | Recorded behavior |
|---|---|
| `GET /api/getData` | HTTP 200 for `settings:/version`, updater action metadata and updater status |
| `GET /api/getRows` | HTTP 200 for updater UI rows and UI tree rows |
| `POST /api/setData` | The two selected updater activation attempts returned HTTP 401 |
| `POST /api/event/modifyQueue` | Some captured web-UI subscriptions returned HTTP 200; not a configuration-write authorization result |
| `GET /api/event/pollQueue` | Long-poll traffic, including HTTP 200 responses; some requests have no paired response in the capture |

Other observed requests include `/index.fcgi`, `/fts.fcgi`, `/settings.fcgi`, `/jsapi/nsdk-api.js`, `/jsapi/common.js`, `/webclient` and `/setup/eureka_info`. A request's presence alone does not establish a successful route or supported public API.

[Selected wire observations](../data/http-observations.json) preserve request/response frame numbers and bodies, with host/transport identifiers and authentication parameters omitted. [Evidence notes](evidence.md) identify the private source captures.

## Read-only reproduction

Use the device's current LAN address. The documentation address below is intentionally non-routable; replace it. No credentials, update activation or settings changes are sent by these examples.

```sh
export RADIO='http://192.0.2.1'  # Replace with your radio's current LAN address.
curl --fail --silent --show-error --max-time 15 --get   --data-urlencode 'path=settings:/version'   --data-urlencode 'roles=@all'   "$RADIO/api/getData"
```

The captured response was HTTP 200:

```json
{
  "defaultValue": {"type": "string_", "string_": "1.0.22.0xefb17f3"},
  "type": "value",
  "path": "settings:/version",
  "title": "Version",
  "value": {"type": "string_", "string_": "1.0.22.0xefb17f3"}
}
```

Read updater status, without starting a check:

```sh
curl --fail --silent --show-error --max-time 15 --get   --data-urlencode 'path=firmwareupdate:updateStatus'   --data-urlencode 'roles=@all'   "$RADIO/api/getData"
```

The captured value was `{"type":"firmwareUpdateStatus","firmwareUpdateStatus":{"state":"idle"}}` inside a node's `value` field.

Read the updater UI container:

```sh
curl --fail --silent --show-error --max-time 15 --get   --data-urlencode 'path=firmwareupdate:uiInternet'   --data-urlencode 'roles=@all'   --data-urlencode 'from=0' --data-urlencode 'to=20'   "$RADIO/api/getRows"
```

The response has `roles`, `rows`, `rowsCount` and `rowsVersion`. In the captured response, there was one row: an action at `firmwareupdate:uiStartUpdate`, titled **“Check for update”**. Pagination boundary semantics have not been established beyond the recorded request.

These command shapes are grounded in recorded HTTP exchanges. During repository preparation, a read-only connection to the last recorded address was refused; **no current live-device result is claimed**.

## Nodes, roles and typed values

`settings:/version` is a node identifier passed as a query parameter, not a filesystem path or an HTTP route. The colon and slash placement is significant: updater nodes use forms such as `firmwareupdate:updateStatus`.

Observed typed values include `string_`, `i32_`, `bool_` and named types such as `webserverAuthMode`. `roles=@all` yielded a full node object in selected exchanges; `roles=value` yielded lists in other captured requests. Do not assume every request has the same response shape.

The saved collector stores read failures as `{"_err":"HTTP Error 500: Internal Server Error"}`. That is a collector representation, not the server's literal error body. Recorded guessed nodes `settings:/system/firmwareVersion`, `settings:/system/version` and `nsdk:/version` returned HTTP 500; use the observed `settings:/version` node instead.

## Authentication and failed writes

The saved settings report:

- HTTP port `80`, HTTPS port `443`, document root `/www/pages`.
- `authMode = setData`, described as **“Configuration changes”**.
- `strict = true`; `httpsUseDeviceCert = true`.

Those settings do not by themselves establish every active listener, TLS trust behavior or an authorization bypass.

The capture provides stronger, narrower evidence: both of these historical `POST /api/setData` JSON bodies received **HTTP 401**, a `WWW-Authenticate` challenge beginning **`HMAC_SHA256,`**, and a JSON error message **`Forbidden`**:

```json
{"path":"firmwareupdate:uiStartUpdate","role":"activate","value":{}}
```

```json
{"path":"firmwareupdate:checkForUpdate","role":"activate","value":{}}
```

These are **failed experiment records, not runnable instructions or a recommendation to retry**. Challenge parameters and authorization material are deliberately excluded. The signing input, key provisioning and official app's authorization flow remain unverified here. Do not label this Basic/Digest authentication or claim a known key.

Reading `firmwareupdate:checkForUpdate` returned `{"type":"action","path":"firmwareupdate:checkForUpdate"}`. **Reading an action is not activating it.** Likewise, `modifiable: true` does not prove that a write is authorized or that it will persist.

## Candidate paths are not implemented features

The settings snapshot includes:

- `ui:/debug/testPlayback/url`: empty string, marked modifiable.
- `settings:/spotify/presets`: value `{"presets":[]}`, marked modifiable.
- `settings:/bleControl/wifiServiceUuid`: `CEE499E3-43A8-51D2-E7F4-1626AD235C0F`, titled “Wifi Pairing Service UUID”.

The UUID is a protocol setting, not a published device-unique ID. Its presence is not a captured GATT service discovery or a decoded pairing protocol.

None of these entries proves arbitrary URL playback, NTS button remapping, persistent station configuration or successful BLE control. Spotify presets must not be conflated with physical NTS controls.

A retained research helper also referred to `/file/stream//tmp/temp_data_logs.tgz.enc`. An encrypted diagnostic archive was saved privately, but its contents and format were not established. It is not a demonstrated firmware image or backup; do not publish it.
