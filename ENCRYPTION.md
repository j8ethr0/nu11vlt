# Encryption Implementation

## ChaCha20-Poly1305 (Primary)

All content encrypted on-device using ChaCha20-Poly1305 AEAD cipher.
swift
// Key generation
var key = Data(count: 32)
_ = key.withUnsafeMutableBytes { SecRandomCopyBytes(kSecRandomDefault, 32, $0.baseAddress!) }

// Encryption
let sealedBox = try ChaChaPoly.seal(data, using: key)


**Properties:**
- 256-bit keys stored in iOS Keychain
- Poly1305 authentication tag
- Files written with `.completeFileProtection`
- Separate keys for Vault, Fade, and Backup vaults

## AES-256-GCM (Archives)

Password-derived encryption for archive files using Argon2id.
swift
// Argon2id key derivation (illustrative)
import SwiftSodium

let sodium = Sodium()
let password = "password".bytes
let salt = sodium.randomBytes.buf(length: 16)!

// Derive a 32-byte key from the password
let key = sodium.pwHash.hash(
    outputLength: 32,
    password: password,
    salt: salt,
    opsLimit: UInt64(2),
    memLimit: UInt64(67108864)
)!

// AES encryption
let sealedBox = try AES.GCM.seal(data, using: SymmetricKey(data: key))


## Archive Modes

**Portable:** ChaCha20 → decrypt → AES-256 (password-derived via Argon2id)  
**Secure:** ChaCha20 → AES-256 (double encrypted, device-locked)

## File Format

`.n11` / `.n11note` - ChaCha20-Poly1305 encrypted  
`.nu11vlt` - ZIP archive with AES-256-GCM encrypted files

## Standards

IETF RFC 8439 | NIST FIPS 197 | RFC 9106 (Argon2)
