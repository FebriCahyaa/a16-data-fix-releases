## v1.7.0 (2026-11-22)

- Added an **Updates** tab to the webroot. It shows a build-time snapshot of the
  latest source commits and releases-repository issues. Private source repos
  show commit text without a link, so the ZIP contains no dead URLs.
- Added **Export issue**. It turns the last scan, recent runs and the build
  snapshot into a ready-to-send report, highlights detected faults, and opens a
  prefilled Telegram message to @Fbrichyyy. Copy and `.txt` download are also
  available; the WebUI does not send the message by itself because that would
  need a bot token.
- `webroot/data/updates.json` is generated in CI and hashed. The generator
  scrubs text against the KernelSU-Next risk catalog before writing it, since
  ksud scans every ZIP entry as text. Only `report.json` and `history.jsonl`
  stay excluded from the SHA256 manifest.

## v1.6.1 (2026-11-22)

- Stated Android 17 support explicitly in `module.prop`, the config header, the
  README and the security notes. The repair itself is unchanged: Android 16 and
  17 share the same native create-only path, with no version-gated behavior.
- The webroot footer now names Android 16 and 17 as target releases, so the
  supported range is visible without reading the README.

## v1.6.0 (2026-11-22)

- Renamed the module to **QPR Storage Fix**. The module id, ZIP filename and OTA
  URL stay the same, so existing installs keep receiving updates. `module.prop`
  now has a plain description and a separate `copyright` field.
- Android detection now records release, SDK, codename, build ID and preview SDK.
  The QPR label is shown with the security patch date and build ID that produced
  it, instead of only a bare estimate.
- Added an offline webroot dashboard at `webroot/index.html`. It shows the last
  scan, repair counters, Android/QPR, internal storage, Zygisk and metamodule
  state, plus a 40-entry history table and the raw JSON report. The layout uses
  a compact dark card style with three tabs.
- `a16fixd` writes `webroot/data/report.json` and `history.jsonl` after each
  boot scan and Action press. Both are runtime state and are excluded from the
  SHA256 manifest and integrity check; the web UI sources are still hashed.

## v1.5.0 (2026-11-22)

- **Module is now focused only on the Android 16+ QPR storage bug.** Removed the
  RIL, mount, filesystem-repair, property-injection, custom-patch and encrypted
  payload code, plus their config toggles and documentation. The ZIP now ships
  three dynamic modules instead of six.
- **Automatic detection added to the Action report and `module.log`:**
  - internal storage type read directly from `/proc/mounts` (`f2fs`, `ext4` or
    `unknown`, with source device and mount options);
  - installed Zygisk implementation and version (Magisk built-in, ZygiskNext,
    ReZygisk, NeoZygisk or native bridge), plus the compiled Zygisk API level
    (`v5`);
  - active metamodule state, while the module itself still never sets
    `metamodule=1`.
- Config reduced to `FIX_ANDROID_DATA=1` and `REQUIRE_SHA256=1`. Detection needs
  no toggles. Existing runtime configs inherit these defaults.
- ABI bumped to 4 because the shared config/context structures were reduced.
- Installer description renamed to **A16 Data Fix (Storage QPR)**.

## v1.4.0 (2026-11-23)

- **Android/data storage repair is now implemented and enabled by default.**
  On Android 16/QPR ROMs where `vold_prepare_subdirs` is denied, the per-app
  external folders are never created and apps fall back to internal storage.
  At `post-fs-data` and `boot-completed` the native fixes module enumerates all
  installed packages from `/data/data`, `/data/app` and `/data/user/<n>` and
  creates only the **missing** `/data/media/0/Android/{data,obb,media}/<pkg>`
  folders with `media_rw:media_rw`, mode `0777` and `media_rw_data_file`.
- Existing folders are never modified, so the pass cannot break working apps.
  Results appear in `module.log` and in the Action report:
  `Android/data pass : scanned=N created=M failed=K`.
- `FIX_ANDROID_DATA=1` added to `config.conf`. Existing runtime configs from
  older releases inherit the enabled default.
- ABI bumped to 3 because the config/context structures gained fields.

## v1.3.7 (2026-11-23)

- Removed the KernelSU-Next security-rule warning. Its scanner reads every ZIP
  entry as text, including compiled `.so` files, and matched a third-party
  mount metamodule ID that was only present as a detection-only string in
  `liba16fix_core.so`. That ID is no longer listed; the `/data/adb/metamodule`
  symlink and the other metamodule IDs are still detected.
- Added `tools/check_ksu_risk.py` to CI. It mirrors ksud's tokenizer and fails
  the build if any ZIP entry matches the current KernelSU-Next risk catalog,
  so a plain literal can never silently reintroduce the warning.

## v1.3.6 (2026-11-23)

- Installer output is shorter: `DEBUG` lines are no longer written to stderr,
  so root managers no longer interleave `loaded dynamic module` messages with
  the on-screen report. They remain in `module.log` and logcat.

## v1.3.5 (2026-11-23)

- **Critical integrity fix:** `sha256_final()` did not transform the final
  padding block when a file size was not a multiple of 64 bytes. This made 44
  of 46 files appear as `CHANGED` after a clean install. Only files sized
  exactly 4032 bytes (63x64) verified correctly.
- The same bug affected HMAC-SHA256 validation of encrypted `.so.enc` modules;
  it is fixed by the same change.
- Added a known-answer SHA256 self-test ("abc") at crypto init. On failure the
  integrity engine is marked `sha256_selftest_failed` and every patch is
  rejected.
- Installer banner now reads the real version from `module.prop`.

## v1.3.4 (2026-11-22)

- Fixed the real install-time failure: KernelSU-Next extracts `bin/<abi>/` files
  without the executable bit. All trampolines now restore `chmod 0755` and, if
  the filesystem is mounted `noexec`, copy the core to `/data/local/tmp` before
  running it.
- The install trace now includes `ls -l` of the ABI directory so permissions
  are visible.

## v1.3.3 (2026-11-22)

- `customize.sh` can no longer fail an installation: native is tried first, then
  a `sha256sum` fallback reports changed files, and the script always exits 0.
- Added `/data/local/tmp/a16fix_install_trace` to confirm the installer script
  is reached and to record the native exit code.
- Native output is now flushed immediately so installer messages are not lost.

## v1.3.2 (2026-11-22)

- Fixed installation failure on KernelSU-Next / Hybrid Mount: shell trampolines
  now fall back to `MODDIR`, the current directory, and an ABI scan of `bin/*/`
  when `MODPATH` or `ro.product.cpu.abi` is unavailable.
- Install-time integrity reporting is now non-fatal. Changed files are still
  named with expected/actual hashes, but managers that rewrite files during
  extraction no longer break installation. Custom patches remain rejected.
- Added an explicit error message when the native core cannot be located.

## v1.3.1 (2026-11-22)

- Added a dedicated `verify.sh` entry point: run it from the module manager to
  list every file that is `CHANGED`, `MISSING`, or `UNLISTED`.
- Installation now runs the SHA256 check **first** and prints the exact path,
  expected hash, and actual hash of each modified file before aborting.
- `a16fixctl verify` now prints `expected` and `actual` hashes for changed files.
- Installer and verify stages force integrity checking even if the running
  config had `REQUIRE_SHA256=0`.
- Bumped internal dynamic-module ABI to 2 for the new detail callback.

## v1.3.0 (2026-11-22)

- Full native refactor: all runtime stages now run in `a16fixd` (C++17, NDK r27d).
- Shell files reduced to four-line trampolines; no runtime shell logic remains.
- Dynamic module ABI (`a16fix_module_init`) with `crypto`, `core`, `fixes` and
  three `custom_*` templates, loaded with `dlopen()` per ABI.
- Clean runtime layout: `bin/<abi>/`, `lib/<abi>/`, `zygisk/<abi>.so`,
  `config/config.conf`.
- SHA256 verification is now performed natively before any fix runs.
- Encrypted custom modules (`*.so.enc`) use AES-256-CBC + HMAC-SHA256.
- Documentation, workflow messages and all module text are fully in English.

## v1.2.0 (2026-11-22)

- Real native `a16fixctl` (3 ABIs) and Zygisk v5 module from NDK r27d.
- SHA256 verification via `a16fixctl verify`, with `sha256sum` fallback.
- Payload encryption with AES-256-CBC + HMAC-SHA256; keyfile not shipped.
- `updateJson` added; OTA published to a separate public releases repository.
- Private source repository, public artifact/OTA repository.

## v1.1.0 (2026-11-22)

- Zygisk and metamodule detection, `ZYGISK_REQUIRED`.
- Anti-tamper SHA256 manifest and obfuscated protected payload.
- Copyright notice for @FebriCahyaa.

## v1.0.0

- Diagnostic framework for `/data` mounts, RIL and F2FS/EXT4 on Android 16/17+.
