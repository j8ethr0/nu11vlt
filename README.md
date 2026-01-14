# Nu11VLT

Zero-knowledge photo and note vault for iOS with military-grade encryption.

🌐 [dro1d.org/nu11vlt.html](https://dro1d.org/nu11vlt.html) | 📄 [Technical Specs](https://dro1d.org/nu11vlt-tech.html)

## Features

- **ChaCha20-Poly1305** encryption (256-bit keys)
- **DoD 5220.22-M** secure deletion (3-pass overwrite)
- **Skeleton Switch** auto-wipe after configurable inactivity
- **Dual backup modes**: Portable (AES-256) or Secure Archive (double encryption)
- **Decoy vault** with backup PIN
- Face ID/Touch ID + optional PIN authentication
- Self-destructing "Fade" storage
- Local-only storage (no cloud sync)

## Security

### Encryption Stack
- On-device: ChaCha20-Poly1305 AEAD
- Backup: AES-256-GCM with PBKDF2 (100k iterations)
- Keys: iOS Keychain (Secure Enclave)

### Threat Model
✓ Physical theft, forensic recovery, unauthorized access, forced unlock, extended seizure  
✗ Jailbroken devices, iOS zero-days, weak passwords

## Standards

- ChaCha20-Poly1305: IETF RFC 8439
- AES-256-GCM: NIST FIPS 197
- PBKDF2: NIST SP 800-132
- Secure Deletion: DoD 5220.22-M

## Requirements

iOS 18.0+ | SwiftUI | SwiftData

## License

Copyright © 2025-2026. All rights reserved.
