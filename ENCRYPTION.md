# Encryption Implementation

## ChaCha20-Poly1305 (Primary)

All content encrypted on-device using ChaCha20-Poly1305 AEAD cipher.

```swift
// Key generation
var key = Data(count: 32)
_ = key.withUnsafeMutableBytes { SecRandomCopyBytes(kSecRandomDefault, 32, $0.baseAddress!) }

// Encryption
let sealedBox = try ChaChaPoly.seal(data, using: key)
```

**Properties:**
- 256-bit keys stored in iOS Keychain
- Poly1305 authentication tag
- Files written with `.completeFileProtection`
- Separate keys for Vault, Fade, Decoy

## AES-256-GCM (Backups)

Password-derived encryption for backup files.

```swift
// PBKDF2 key derivation
let salt = Data(count: 32)
_ = salt.withUnsafeMutableBytes { SecRandomCopyBytes(kSecRandomDefault, 32, $0.baseAddress!) }

let key = try PBKDF2.deriveKey(
    from: password,
    salt: salt,
    iterations: 100_000,
    keyLength: 32,
    using: .sha256
)

// AES encryption
let sealedBox = try AES.GCM.seal(data, using: key)
```

## Backup Modes

**Portable:** ChaCha20 → decrypt → AES-256 (password-based)  
**Secure Archive:** ChaCha20 → AES-256 (double encrypted, device-locked)

## File Format

`.n11` / `.n11note` - ChaCha20-Poly1305 encrypted  
`.nu11vlt` - ZIP archive with AES-256-GCM encrypted files

## Standards

IETF RFC 8439 | NIST FIPS 197 | NIST SP 800-132
