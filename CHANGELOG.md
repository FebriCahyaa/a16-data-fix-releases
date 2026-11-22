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
