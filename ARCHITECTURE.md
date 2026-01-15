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
            ├─> 256-bit Key (Secure Enclave)
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
Camera/Photos → UIImage → Data → ChaCha20 Encrypt → .n11 File → Documents Directory
                                      ↑
                                      │
                              SymmetricKey (Keychain)
```

### Photo Viewing

```text
.n11 File → ChaCha20 Decrypt → Data → UIImage → Display
                  ↑
                  │
          SymmetricKey (Keychain)
```

### Secure Deletion

```text
[Standard Mode]
.n11 File → Keychain.delete(key) → FileManager.removeItem()
            (crypto-shredding)

[Secure Mode - Pro]
.n11 File → Random Overwrite → Keychain.delete(key) → FileManager.removeItem()
            (NIST 800-88)
```

---

## Storage Structure

```text
Documents/
├── Vault/
│   ├── Albums/
│   │   └── {AlbumID}/
│   │       └── {PhotoID}.n11
│   └── Photos/
│       └── {PhotoID}.n11
├── Fade/
│   └── {PhotoID}.n11
├── Backup/
│   └── {PhotoID}.n11
└── Notes/
    ├── vault_{NoteID}.n11note
    └── fade_{NoteID}.n11note
```

---

## Key Management

| Key          | Purpose                           | Storage                                                       | Lifecycle                                          |
| ------------ | --------------------------------- | ------------------------------------------------------------- | -------------------------------------------------- |
| Vault        | Encrypts permanent photos & notes | iOS Keychain (`kSecAttrAccessibleWhenUnlockedThisDeviceOnly`) | Created on first launch, destroyed on app deletion |
| Fade         | Encrypts self-destructing content | iOS Keychain                                                  | Separate from Vault Key                            |
| Backup (Pro) | Encrypts alternative vault        | iOS Keychain                                                  | Created when Backup PIN is set                     |

---

## Authentication Flow

```text
App Launch
    ↓
[Face ID Enabled?]
    ├─ Yes → Face ID Challenge
    │         ├─ Success → Unlock
    │         └─ Fail → PIN Prompt
    └─ No → PIN Prompt
              ├─ Primary PIN → Load Vault Key
              └─ Backup PIN → Load Backup Key
```

---

## Skeleton Switch (Dead Man's Switch)

```text
App Launch
    ↓
Check Last Check-In Timestamp (Keychain)
    ↓
[Exceeded Interval?]
    ├─ No → Update Timestamp → Continue
    └─ Yes → Trigger Wipe
              ↓
         [Secure Mode?]
              ├─ Yes → NIST 800-88 Overwrite → Delete Keys → Delete Files
              └─ No → Delete Keys → Delete Files
```

---

## Archive Export

### Portable Archive

```text
Vault Photos → ChaCha20 Decrypt → AES-256 Encrypt (Password-Derived Key) → .nu11vlt ZIP
                    ↑                           ↑
              Vault Key (Keychain)      Argon2id(password, salt)
```

### Secure Archive

```text
Vault Photos → Keep ChaCha20 Encrypted → AES-256 Encrypt (Device Key) → .nu11vlt ZIP
                         ↑                              ↑
                   Vault Key                    Archive Key (Keychain)
```

---

## Network Isolation

Nu11VLT has **zero network code**. All encryption, storage, and processing happens on-device.

```text
Info.plist:
  - No NSAppTransportSecurity key
  - No network entitlements
  - iCloud backup excluded for Documents directory
```

---

## Performance Considerations

* **Thumbnail Caching:** Decrypted thumbnails cached in memory (LRU eviction)
* **Lazy Decryption:** Photos only decrypted when viewed
* **Background Fade Deletion:** Timer-based cleanup runs on app launch
* **Secure Wipe:** Optional Pro feature (slower, more thorough)

---

## Security Boundaries

**Protected:**

* Data at rest (encrypted with ChaCha20)
* Keys in Secure Enclave (hardware-backed)
* Forensic recovery (crypto-shredding + optional overwrite)

**Not Protected:**

* Data in RAM while app is unlocked
* Screenshots while app is active (mitigated with blur overlay)
* Compromised iOS (zero-day exploits)
* Jailbroken devices

---
