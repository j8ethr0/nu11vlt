# Encrypted Archives

Archive export/import lets you back up your vault to a single encrypted file, or move it to a new device. This page covers what's actually protected, and the two modes available.

---

## Sealed Archives — the index is encrypted too

A naive encrypted backup might encrypt your photos and notes but leave the *list* of what's inside — file names, note titles, item counts, dates — readable without the password. Nu11VLT's archives don't do that.

The archive's internal index (the equivalent of a table of contents: what's in the archive, what it's called, when it was created) is encrypted as a unit, separately from being merely "inside" an encrypted container. Without the correct password, an archive file reveals:

- No file names or note titles
- No item counts
- No dates
- No way to distinguish an archive with 3 items from one with 3,000

The only thing stored unencrypted is the random salt the password-derived key is built from — standard practice, and on its own it tells an attacker nothing about the contents.

## Archive Encryption

Archives use **AES-256-GCM**, deliberately different from the ChaCha20-Poly1305 used for on-device storage. The key is derived from your archive password using **Argon2id** (the same memory-hard hashing used for your PIN, with stronger parameters — see [ENCRYPTION.md](./ENCRYPTION.md)).

Using a different cipher for archives than for on-device storage is intentional defense-in-depth: a weakness found in one algorithm's implementation doesn't automatically expose the other.

## Two Modes

### Secure (device-locked)

Keeps your content wrapped in its original on-device encryption, then adds the password-derived AES-256-GCM layer on top. Restoring a Secure archive requires *both* the password *and* the original device's vault key — it cannot be restored on a different phone. Best for local encrypted backups you don't intend to move elsewhere.

### Portable (cross-device)

Re-encrypts your content directly under the password-derived key, with no dependency on the originating device. Restorable on any device, as long as you have the password. Best for migrating to a new phone.

Both modes get the same sealed index protection described above — the difference is only in how the content payload is bound (to your device, or just to your password).

## What's Included

Archives include your Vault photos, albums, and notes (including Fade notes), preserving album membership and item metadata on restore.

## Atomic Imports

If an archive import is interrupted — app closed, crash, low storage — your vault is not left in a corrupted or partially-restored state. An import either completes in full or leaves your existing vault data untouched; there's no in-between state where some photos or notes show up without the rest.

---

## Standards

* **AES-256-GCM:** NIST FIPS 197 / NIST SP 800-38D
* **Argon2id:** RFC 9106
