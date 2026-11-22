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
