# Nu11VLT

Zero-knowledge photo and note vault for iOS with ChaCha20-Poly1305 encryption.

🌐 [dro1d.org/nu11vlt.html](https://dro1d.org/nu11vlt.html) | 📄 [Technical Specs](https://dro1d.org/nu11vlt-tech.html)

## Features

- **ChaCha20-Poly1305** encryption (256-bit keys), one key per vault context
- **Argon2id** key derivation for PIN and archive passwords (memory-hard, GPU-resistant)
- **Encrypted thumbnail cache** — even cached previews are encrypted at rest and excluded from device backups
- **Sealed archive exports** — encrypted archives hide file names, note titles, counts, and dates, not just content
- **Crypto-shredding** secure deletion, with optional supplementary overwrite pass
- **Skeleton Switch** — auto-wipe after configurable inactivity
- **Dual archive modes**: Portable (cross-device) or Secure (device-locked)
- **Backup PIN** for duress/plausible deniability
- FaceID + optional PIN authentication
- Self-destructing "Fade" storage
- Local-only — no cloud sync, no telemetry, no analytics

## Security

### Encryption Stack
- On-device: ChaCha20-Poly1305 AEAD
- Archive: AES-256-GCM with **Argon2id** key derivation
- Keys: iOS Keychain, hardware-protected via Secure Enclave-rooted Data Protection — never extractable, never synced, never leave the device

### Threat Model
✓ Physical theft, forensic recovery, unauthorized access, forced unlock, extended seizure  
✗ Jailbroken devices, iOS zero-days, weak passwords

See [THREAT_MODEL.md](./THREAT_MODEL.md) for detailed analysis.

## Documentation

- [ARCHITECTURE.md](./ARCHITECTURE.md) - System architecture and data flow
- [ENCRYPTION.md](./ENCRYPTION.md) - Encryption implementation details
- [ARCHIVES.md](./ARCHIVES.md) - Encrypted archive export/import
- [SECURE_WIPE.md](./SECURE_WIPE.md) - Secure wipe process
- [SKELETON_SWITCH.md](./SKELETON_SWITCH.md) - Auto-wipe system
- [BACKUP_PIN.md](./BACKUP_PIN.md) - Plausible deniability feature
- [THREAT_MODEL.md](./THREAT_MODEL.md) - Complete threat assessment
- [COMPARISONS.md](./COMPARISONS.md) - Nu11VLT vs alternatives
- [FAQ.md](./FAQ.md) - Frequently asked questions

## Standards

- ChaCha20-Poly1305: IETF RFC 8439
- AES: NIST FIPS 197 (GCM mode: NIST SP 800-38D)
- Argon2id: RFC 9106
- Crypto-shredding: aligned with NIST SP 800-88 Rev. 1 guidance for flash storage

## Requirements

iOS 26.2+

## Documentation accuracy

This documentation is reviewed against the shipping app on every update. Last verified against **app version 01.2.01** on 2026-06-30.

## License

Copyright © 2025-2026. All rights reserved.

This repository contains documentation only. Source code is proprietary.
