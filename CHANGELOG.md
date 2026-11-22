## v1.2.0 (2026-11-22)

- Native nyata: `a16fixctl` (EXECUTABLE, 3 ABI) dan modul Zygisk v5
  `liba16fix_zygisk.so` → `zygisk/<abi>.so`.
- Build via GitHub Actions memakai Android NDK r27; artefak diverifikasi sebagai ELF.
- Verifikasi SHA256 memakai `a16fixctl verify`, fallback ke `sha256sum`.
- Enkripsi payload: AES-256-CBC + HMAC-SHA256 (encrypt-then-MAC) lewat
  `a16fixctl decrypt`, keyfile tidak dikirim di ZIP.
- `updateJson` ditambahkan; OTA dipublish ke repo releases terpisah.
- Repo proyek privat, repo artefak/OTA publik.

## v1.1.0 (2026-11-22)

- Deteksi Zygisk & metamodule, `ZYGISK_REQUIRED`.
- Manifest SHA256 anti-tamper, protected payload (obfuscation).
- Copyright @FebriCahyaa.

## v1.0.0

- Framework diagnostik mount `/data`, RIL, F2FS/EXT4 untuk Android 16/17+.
