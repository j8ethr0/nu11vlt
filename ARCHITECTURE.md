# Architecture and Encryption Overview

## System Overview

```text
┌─────────────────────────────┐
│       YOUR DEVICE           │
│          (iPhone)           │
└─────────────────────────────┘
            │
            ▼
   [ChaCha20-Poly1305 Encryption]
            │
            ├─> 256-bit Key (iOS Keychain)
            │
            ▼
    ENCRYPTED FILE (.n11)
            │
  ┌─────────┴──────────┐
  ▼                    ▼
[Vault]             [Fade]

┌─────────────────────────────┐
│          OUR SERVERS        │
└─────────────────────────────┘
      [ ERROR: 404 NOT FOUND ]
      [ NO SERVERS USED ]
```

---

## Data Flow

### Photo Import

```text
Camera/Photos → Image Data → ChaCha20 Encrypt → .n11 File → On-device storage
                                    ↑
                                    │
                            SymmetricKey (Keychain)
```

### Photo Viewing

```text
.n11 File → ChaCha20 Decrypt → Image Data → Display
                  ↑
                  │
          SymmetricKey (Keychain)
```

### Secure Deletion

```text
[Standard Mode]
.n11 File → Destroy encryption key → Delete file
            (crypto-shredding)

[Secure Mode]
.n11 File → Random overwrite → Destroy encryption key → Delete file
            (supplementary obfuscation pass)
```

---

## Storage Structure

Each vault context (Vault, Fade, Backup) has its own storage area on-device, encrypted independently with its own key. Album membership and note metadata are tracked locally and resolved at read time — they are not exposed in plaintext file or folder names.

---

## Key Management

| Key          | Purpose                           | Storage                                                     | Lifecycle                                          |
| ------------ | ---------------------------------- | ------------------------------------------------------------ | --------------------------------------------------- |
| Vault        | Encrypts permanent photos & notes | iOS Keychain, accessible only on this device while unlocked | Created on first launch, destroyed on app deletion |
| Fade         | Encrypts self-destructing content | iOS Keychain, accessible only on this device while unlocked | Independent from the Vault key                     |
| Backup       | Encrypts the alternative vault    | iOS Keychain, accessible only on this device while unlocked | Created when a Backup PIN is set                   |

Keys never sync via iCloud Keychain and never migrate off the device — even a full device backup cannot extract them. See [ENCRYPTION.md](./ENCRYPTION.md) for how key storage is hardware-protected.

---

## Authentication Flow

```text
App Launch
    ↓
[FaceID Enabled?]
    ├─ Yes → FaceID Challenge
    │         ├─ Success → Unlock
    │         └─ Fail → PIN Prompt
    └─ No → PIN Prompt
              ├─ Primary PIN → Load Vault Key
              └─ Backup PIN → Load Backup Key
```

A successful unlock requires the *full* chain to complete (FaceID and PIN, if both are configured) before the app is considered authenticated — a partial success (e.g. FaceID alone, with PIN still pending) does not unlock the vault.

---

## Skeleton Switch (Dead Man's Switch)

```text
App Launch
    ↓
Check time since last successful check-in
    ↓
[Exceeded configured interval?]
    ├─ No → Reset check-in timer → Continue
    └─ Yes → Trigger wipe
              ↓
         [Secure Mode?]
              ├─ Yes → Overwrite → Destroy keys → Delete files
              └─ No → Destroy keys → Delete files
```

See [SKELETON_SWITCH.md](./SKELETON_SWITCH.md) for details and limitations.

---

## Archive Export

Nu11VLT supports encrypted archive export/import for backup and device migration, including a fully encrypted (“sealed”) archive index. See [ARCHIVES.md](./ARCHIVES.md) for the full breakdown.

---

## Audit Trail

Nu11VLT keeps a local security event log: authentication attempts, vault unlocks, content changes, archive exports/imports, Skeleton Switch activity, and screenshot detections are all timestamped and recorded for your own review.

- Stored only within the app's local data — never transmitted anywhere, there's nothing to transmit it to
- Filterable by category and severity within the app
- Configurable retention period
- Exportable to plain text or CSV for your own records

---

## Network Isolation

Nu11VLT has **zero network code** for any vault functionality. All encryption, storage, and processing happens on-device.

```text
- No outbound network requests anywhere in the app
- No analytics, telemetry, or crash-reporting SDKs
- Vault, Fade, Backup, and thumbnail-cache directories excluded from iCloud/device backup
```

A network-status indicator is shown to the user purely as an informational signal — it does not mean the app is using the network.

---

## Performance Considerations

* **Thumbnail Caching:** Two-tier cache — decrypted thumbnails held briefly in memory for scroll performance, plus a persistent on-device cache encrypted with the vault key and excluded from backup. See [ENCRYPTION.md](./ENCRYPTION.md).
* **Lazy Decryption:** Photos and notes are only decrypted when viewed.
* **Background Fade Deletion:** Expired Fade content is cleaned up automatically, both on a timer while the app is open and on launch.
* **Secure Wipe:** Optional, user-enabled (slower, more thorough than standard deletion).

---

## Security Boundaries

**Protected:**

* Data at rest (encrypted with ChaCha20-Poly1305)
* Cached thumbnails (also encrypted at rest)
* Encryption keys (hardware-protected, device-bound, never synced)
* Forensic recovery after deletion (crypto-shredding + optional overwrite)

**Not Protected:**

* Data in memory while the vault is unlocked
* Screenshots while the app is active (detected and logged, not prevented; app-switcher preview is blurred)
* Compromised iOS (zero-day exploits)
* Jailbroken devices

---
