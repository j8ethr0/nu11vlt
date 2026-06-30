# Encryption Implementation

## ChaCha20-Poly1305 (On-device content)

All vault, fade, and backup content is encrypted on-device using the ChaCha20-Poly1305 AEAD cipher (via Apple's CryptoKit).

**Properties:**

* 256-bit keys, randomly generated per vault context
* Poly1305 authentication tag — tampering with ciphertext is detected, not just decryption of bad data
* Files written with complete file protection, so they're inaccessible while the device is locked
* Separate, independent keys for Vault, Fade, and Backup content — compromising one does not expose the others

## Key Storage

Keys are stored in the iOS Keychain, marked accessible only on this device and only while unlocked. That means:

* Keys never sync via iCloud Keychain
* Keys never migrate to another device, even via a full device backup/restore
* The Keychain's protection is hardware-rooted in the device's Secure Enclave through iOS's Data Protection key hierarchy — keys cannot be extracted without unlocking the device first

## Encrypted Thumbnail Cache

Cached photo thumbnails are not stored as plaintext previews. Each cached thumbnail is encrypted with the same vault key as the full-resolution content, and the cache directory is excluded from iCloud and device backups — so a backup of your phone doesn't carry a readable preview of your vault.

## Password / PIN Hashing — Argon2id

PIN validation and archive password derivation both use Argon2id, the memory-hard password-hashing algorithm designed to resist GPU and ASIC cracking attempts.

| Use Case          | Relative Cost | Goal                                              |
| ------------------ | ------------- | -------------------------------------------------- |
| PIN Hashing         | Lower          | Fast enough for real-time entry, still GPU-resistant |
| Archive Passwords   | Higher (~1s)   | Stronger protection for offline brute-force resistance against archive files |

Archive passwords require a minimum length; there are no other complexity rules enforced by the app — pick a long, unique password if you export an archive.

## Archive Encryption

Archive exports use a different cipher (AES-256-GCM) from on-device storage, layered with Argon2id-derived keys, and the archive's internal index is itself encrypted. See [ARCHIVES.md](./ARCHIVES.md) for the full breakdown of archive formats and modes.

## File Types

* `.n11` — individual encrypted photo files
* `.n11note` — individual encrypted note files
* `.nu11vlt` — encrypted archive (export/backup), see [ARCHIVES.md](./ARCHIVES.md)

These are container formats only — file extensions don't reveal anything about content, since everything inside is ciphertext.

---

## Standards

* **ChaCha20-Poly1305:** IETF RFC 8439
* **AES:** NIST FIPS 197 (GCM authenticated mode: NIST SP 800-38D)
* **Argon2id:** RFC 9106
* **Key Storage:** iOS Keychain, hardware-protected via the Secure Enclave-rooted Data Protection key hierarchy
* **Data Destruction:** Crypto-shredding aligned with NIST SP 800-88 Rev. 1 guidance for flash storage — see [SECURE_WIPE.md](./SECURE_WIPE.md)

---
