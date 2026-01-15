# Architecture

## System Overview

###
┌──────────────────────────────────────┐
│       YOUR DEVICE (iPhone)           │
└──────────────────────────────────────┘
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

┌──────────────────────────────────────┐
│             OUR SERVERS              │
└──────────────────────────────────────┘
      [ ERROR: 404 NOT FOUND ]
      [ WE HAVE NO SERVERS   ]
###

## Data Flow

### Photo Import
###
Camera/Photos → UIImage → Data → ChaCha20 Encrypt → .n11 File → Documents Directory
                                      ↑
                                      │
                              SymmetricKey (Keychain)
###

### Photo Viewing
###
.n11 File → ChaCha20 Decrypt → Data → UIImage → Display
                  ↑
                  │
          SymmetricKey (Keychain)
###

### Secure Deletion
###
[Standard Mode]
.n11 File → Keychain.delete(key) → FileManager.removeItem()
            (crypto-shredding)

[Secure Mode - Pro]
.n11 File → Random Overwrite → Keychain.delete(key) → FileManager.removeItem()
            (NIST 800-88)
###

## Storage Structure

###
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
###

## Key Management

### Vault Key
- **Purpose:** Encrypts permanent photos and notes
- **Storage:** iOS Keychain (`kSecAttrAccessibleWhenUnlockedThisDeviceOnly`)
- **Lifecycle:** Created on first launch, destroyed on app deletion

### Fade Key
- **Purpose:** Encrypts self-destructing photos and notes
- **Storage:** iOS Keychain (same access level)
- **Lifecycle:** Separate from vault key for isolation

### Backup Key (Pro)
- **Purpose:** Encrypts alternative vault (plausible deniability)
- **Storage:** iOS Keychain (same access level)
- **Lifecycle:** Created when Backup PIN is set

## Authentication Flow

###
App Launch
    ↓
[Face ID Enabled?]
    ├─ Yes → Face ID Challenge
    │         ├─ Success → Unlock
    │         └─ Fail → PIN Prompt
    └─ No → PIN Prompt
              ├─ Primary PIN → Load Vault Key
              └─ Backup PIN → Load Backup Key
###

## Skeleton Switch (Dead Man's Switch)

###
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
###

## Archive Export

### Portable Archive
###
Vault Photos → ChaCha20 Decrypt → AES-256 Encrypt (Password-Derived Key) → .nu11vlt ZIP
                    ↑                           ↑
              Vault Key (Keychain)      Argon2id(password, salt)
###

### Secure Archive
###
Vault Photos → Keep ChaCha20 Encrypted → AES-256 Encrypt (Device Key) → .nu11vlt ZIP
                         ↑                              ↑
                   Vault Key                    Archive Key (Keychain)
###

## Network Isolation

Nu11VLT has **zero network code**. All encryption, storage, and processing happens on-device.

###
Info.plist:
  - No NSAppTransportSecurity key
  - No network entitlements
  - iCloud backup excluded for Documents directory
###

## Performance Considerations

- **Thumbnail Caching:** Decrypted thumbnails cached in memory (LRU eviction)
- **Lazy Decryption:** Photos only decrypted when viewed
- **Background Fade Deletion:** Timer-based cleanup runs on app launch
- **Secure Wipe:** Optional Pro feature (slower, more thorough)

## Security Boundaries

**Protected:**
- Data at rest (encrypted with ChaCha20)
- Keys in Secure Enclave (hardware-backed)
- Forensic recovery (crypto-shredding + optional overwrite)

**Not Protected:**
- Data in RAM while app is unlocked
- Screenshots while app is active (mitigated with blur overlay)
- Compromised iOS (zero-day exploits)
- Jailbroken devices
