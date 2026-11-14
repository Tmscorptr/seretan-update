# Seretân Digital Audio — Update Repository

This public repository contains only the files that Seretân Digital Audio devices download.
The application source code is **not** here; it stays private.

| File | Purpose | Used on the device |
|---|---|---|
| `stations.json` | Internet radio list (`name`, `url`, `genre`, optional `logo`) | Radio → Add → From server → Update now |
| `logos/` | Station logo images named in the `logo` field of `stations.json` | Downloaded when a station's logo file is missing on the device |
| `logo-library/` | Logo library: `index.json` (`name`, `file`) and `lib-<name>.png` images (max 256 px). Stations without their own logo get one by name | Downloaded on first start and on every "Update now"; only missing files are fetched |
| `fm.txt` | FM channel list (`Name \| frequency`) | Same screen, FM tab |
| `latest.json` | Published system updates | Settings → System → Check for updates |
| Releases | Signed system update bundles (`.raucb`) | Downloaded from the URL in `latest.json` |

## Where devices read the files

`https://raw.githubusercontent.com/Tmscorptr/seretan-update/main/<file>`

The repository is public, so no extra setup (such as GitHub Pages) is needed. GitHub caches these
URLs for a few minutes, so changes reach devices within about 5 minutes.

## Updating the radio list

Edit `stations.json` and commit. When "Update now" is pressed, devices add new stations and update
existing ones. Stations added by the user are never removed.

## Adding a logo to the library

Put a PNG named `lib-<name>.png` (lowercase letters and digits only) into `logo-library/` and add a line
`{"name": "Station Name", "file": "lib-<name>.png"}` to `logo-library/index.json`. Several names may point to the
same file. Logos are trademarks of their stations.

## Updating the FM list

FM frequencies differ by city, so `fm.txt` should be filled in for the region where the devices are
sold. One channel per line: `Station Name | 99.9`. Lines starting with `#` are ignored.

## Publishing a new system version

1. Build the signed `.raucb` bundle with Buildroot (e.g. `seretan-cm4-0.2.0.raucb`).
2. Repository → **Releases** → new release (`v0.2.0`) → attach the bundle → publish.
3. Add an entry to `latest.json` (one per hardware type):

```json
{
  "bundles": [
    {
      "compatible": "seretan-cm4",
      "version": "0.2.0",
      "url": "https://github.com/Tmscorptr/seretan-update/releases/download/v0.2.0/seretan-cm4-0.2.0.raucb",
      "size": 123456789,
      "sha256": "<sha256 of the bundle>",
      "notes": "Folder playback, A–Z index"
    }
  ]
}
```

- `compatible` must match `/etc/rauc/system.conf` in the image. A device only offers a bundle for its
  own hardware that is **newer** than the installed version.
- To get the `sha256`: on Windows `certutil -hashfile bundle.raucb SHA256`, on Linux `sha256sum bundle.raucb`.
- Bundles are signed. Even if someone else placed a file here, devices would refuse unsigned or
  wrongly signed bundles.
- The same bundle can be installed offline from a USB stick: put the file in the top folder of the
  stick and plug it in.
