# Deadly Input — KillBoard 60 firmware

Firmware images for the **KillBoard 60 (KB60)**, published so [DI Ware](https://github.com/Adrian-MAINGEAR/Deadly-Input-Configurator)
can offer in-app updates. This repository is public because the configurator fetches from it directly
in the browser, which needs CORS — GitHub's raw endpoint sends `access-control-allow-origin: *`.

There is nothing to run here. If you want to configure your keyboard, use DI Ware.

## Files

| File | What it is |
| :---- | :---- |
| `manifest.json` | What DI Ware reads to decide whether an update is available. Always at the same URL |
| `kb60-firmware-r*.bin` | The firmware images themselves. One per release, never overwritten |

## manifest.json

```json
{
  "package": 1,
  "version": 1281,
  "url": "https://raw.githubusercontent.com/Adrian-MAINGEAR/deadly-input-firmware/main/kb60-firmware-r1.bin",
  "sha256": "…",
  "notes": "Shown to the user before they update."
}
```

- **`version`** is the gate, and it is the firmware version word the image actually reports
  (`1293` = "5.13"). DI Ware offers an update whenever this is higher than what the board reports.
  **The image has to be stamped with a real version** — `firmware/mk_version_patch.py` in the
  configurator repo does that in four bytes. An image that reports the stock `1281` can never
  update anybody, which is how release 1 sat here for a month being offered to nobody.
- **`package`** is a monotonic Deadly Input release number, kept as the tiebreaker for a rebuild
  that reuses a version. Bump it every release.
- **`sha256`** is verified before anything is written. A mismatch aborts with nothing flashed.

## Releasing a new build

**Use `firmware/mk_release.py` in the configurator repo.** It writes both files and refuses the
mistakes that are easy to make by hand: a bench image, a known-bad build by hash, a wrong-length or
wrong-chip file, a version at or below stock (which could never update anyone), a version below the
app's own feature gates, a package number that does not beat the published one, and a missing
`sha256`.

```bash
python firmware/mk_release.py --src kb60-firmware-r<N>.bin --notes "What changed, in plain language."
```

Then copy `firmware/publish/` into this repo's root and push. **Never reuse or overwrite a
filename** — old manifests and cached pages may still point at it.

## Recovering an interrupted update

An interrupted update cannot permanently harm the keyboard, but it does leave it unresponsive until
it is power-cycled — **unplug it, wait a few seconds, and plug it back in.** Then open DI Ware and
use **"Keyboard stuck in update mode?"** on the connect screen to finish the update.
