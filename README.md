# Nu11VLT

Zero-knowledge photo and note vault for iOS with military-grade encryption.

🌐 [dro1d.org/nu11vlt.html](https://dro1d.org/nu11vlt.html) | 📄 [Technical Specs](https://dro1d.org/nu11vlt-tech.html)

## Features

- **ChaCha20-Poly1305** encryption (256-bit keys)
- **NIST SP 800-88** secure wipe (single-pass random overwrite)
- **Skeleton Switch** auto-wipe after configurable inactivity
- **Dual archive modes**: Portable (AES-256) or Secure (double encryption)
- **Backup PIN** for duress/plausible deniability
- Face ID/Touch ID + optional PIN authentication
- Self-destructing "Fade" storage
- Local-only storage (no cloud sync)

## Security

### Encryption Stack
- On-device: ChaCha20-Poly1305 AEAD
- Archive: AES-256-GCM with **Argon2id** key derivation
- Keys: iOS Keychain (Secure Enclave)

### Threat Model
✓ Physical theft, forensic recovery, unauthorized access, forced unlock, extended seizure  
✗ Jailbroken devices, iOS zero-days, weak passwords

## Documentation

- [ENCRYPTION.md](./ENCRYPTION.md) - Encryption implementation details
- [SECURE_WIPE.md](./SECURE_WIPE.md) - Secure wipe process
- [SKELETON_SWITCH.md](./SKELETON_SWITCH.md) - Auto-wipe system

## Standards

- ChaCha20-Poly1305: IETF RFC 8439
- AES-256-GCM: NIST FIPS 197
- Argon2id: RFC 9106
- Secure Wipe: **NIST SP 800-88 Rev. 1**

## Requirements

iOS 18.0+

## License

Copyright © 2025-2026. All rights reserved.