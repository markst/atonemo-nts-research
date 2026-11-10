# Contributing

Investigate only devices and networks you own or are authorized to examine. Preserve a working unit and prefer read-only observations. This repository is not a place for credentials, blind flashing recipes or unsupported universal claims.

## Report a finding

Open an issue or pull request containing:

1. **Question:** what behavior were you investigating?
2. **Unit context:** product, board revision and firmware version; omit unique IDs.
3. **Method:** tools/versions, topology and exact sanitized operation.
4. **Expected versus observed:** status codes, typed responses or measured signals.
5. **Evidence:** minimal redacted excerpt/photo, with provenance and a date.
6. **Limits:** what the observation does not prove, including failed attempts.
7. **State changes:** any pairing, settings, playback, firmware or hardware changes and whether/how they were restored.

Use the confidence distinctions in [evidence.md](docs/evidence.md). A setting name is not proof of an enabled feature; reading an action is not running it; a stream playing once is not persistent physical-button remapping.

## Before sharing

- Do not upload raw packet captures, HARs, full settings trees, original camera files, firmware/app binaries or diagnostic archives.
- Remove device MACs/serials/UUIDs, SSIDs/BSSIDs, local addresses/hostnames, account names, pairing data, tokens, credentials, certificate/private-key material and personal filesystem paths.
- Review *values*, default values and nested JSON as well as field names. Prefer an explicit allowlist over attempting to redact a whole dump automatically.
- Crop photos to the relevant hardware; mask QR codes, labels and handwritten identifiers; remove EXIF/GPS and embedded thumbnails. Check the rendered derivative, not just a metadata report.
- Public protocol UUIDs, product regulatory IDs and NTS stream UUIDs may be relevant evidence; distinguish them from unit-unique identifiers and state their source.
- Keep private work under `private/` if it must be inside your checkout. `.gitignore` is a convenience, not a guarantee; already-tracked files remain tracked.
- Review staged content and Git author/email metadata before publishing. If a secret entered Git history, removing it from the current file does not remove it from history; rotate exposed credentials and coordinate history cleanup.

No license is selected yet for original repository material. Do not imply that the repository licenses third-party firmware, audio, trademarks, app code or vendor documents. Settle a reuse license with the owner before accepting contributions under one.

For a potential vulnerability, avoid publishing exploit-enabling secrets or other users' data. Contact the affected vendor privately first and document the finding's exact scope and disclosure status.
