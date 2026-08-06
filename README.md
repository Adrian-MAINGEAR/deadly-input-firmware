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

- **`package`** is the gate — a monotonic Deadly Input release number. DI Ware records it after a
  successful update and offers a new one whenever this number is higher. **Bump it every release.**
- **`version`** is the firmware version word the image reports (`1281` = "5.01"). Keep it truthful;
  don't invent a bump to force an update, that's what `package` is for.
- **`sha256`** is verified before anything is written. A mismatch aborts with nothing flashed.

## Releasing a new build

1. Build the image and name it `kb60-firmware-r<N>.bin` — **never reuse or overwrite a filename**,
   because old manifests and cached pages may still point at it.
2. Commit it here alongside the existing ones.
3. Update `manifest.json`: bump `package`, point `url` at the new file, replace `sha256`, and write
   `notes` in plain language — it is shown to the person deciding whether to update.

```bash
python -c "import hashlib;print(hashlib.sha256(open('kb60-firmware-r1.bin','rb').read()).hexdigest())"
```

## Recovering an interrupted update

An interrupted update cannot permanently harm the keyboard, but it does leave it unresponsive until
it is power-cycled — **unplug it, wait a few seconds, and plug it back in.** Then open DI Ware and
use **"Keyboard stuck in update mode?"** on the connect screen to finish the update.
