# FluidNC-Release

Public mirror of Bantam Tools' FluidNC firmware builds. This repo exists so that every
shipped or pre-release binary has its GPL-3.0 source publicly available and so that
consumers can fetch verified images without credentials. It is written only by CI
(the `bantamtools/FluidNC-Private` release workflow); nothing here is edited by hand.

## Layout

```
releases/<tag>/
  manifest.json                 image list with sizes, flash offsets, SHA-256, and
                                shared_data_commit (the machine-config commit the
                                build used)
  esp32/wifi_s3/firmware.bin    standard build (what Studio and the updater use)
  esp32/wifi_s3/4m/littlefs.bin filesystem image (WebUI + the built-in config.yaml)
  esp32/wifi_s3_usb-otg/...     the USB-OTG variant
  esp32/bootloader.bin, esp32/boot_app0.bin, esp32/4m/partitions.bin
```

Every tag that was ever built appears here, pre-release and release alike. Whether a
tag is a **release** or a **pre-release** is not recorded in this repo: that state
lives on the corresponding GitHub Release in `bantamtools/FluidNC-Private` and
`bantamtools/esp-updater`, and for customer downloads in
`https://files.bantam.tools/plotter/fw/latest.json` (a `release` block and a
`prerelease` block). Promotion from pre-release to release is an explicit workflow in
`bantamtools/esp-updater` ("Promote Firmware Release"); this mirror is not touched by it.

## Machine configs are not here

Machine config YAMLs are not part of this mirror. Their single source of truth is the
private `bantamtools/shared-data` repo; each consumer (the firmware build, the Serama
Firmware Updater, Bantam Tools Studio) pulls them from there at the commit recorded in
`manifest.json` as `shared_data_commit`. The only config in the images is the default
`config.yaml` baked into `littlefs.bin`.

## Consumers

- `bantamtools/esp-updater`: bundles `wifi_s3` images from `releases/<tag>/`, verifying each
  file against `manifest.json`.
- `bantamtools/BT_SVG` (Studio): reads `shared_data_commit` from `manifest.json` for the
  firmware version it tracks; Studio ships no firmware binary.
- Humans: the `manifest.json` `installable` section describes flash layouts for esptool.

## Source

Firmware source for any tag is at `https://github.com/bantamtools/FluidNC-Private` (private;
the GPL source-availability obligation for distributed binaries is met on request and
through the upstream project `bdring/FluidNC` this firmware derives from). The
`source_url` and `release_url` fields in older manifests point at upstream tags and
predate this mirror's role.
