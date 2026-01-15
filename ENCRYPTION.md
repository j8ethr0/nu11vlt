# Encryption Implementation

## ChaCha20-Poly1305 (Primary)

All content encrypted on-device using ChaCha20-Poly1305 AEAD cipher.

```swift
// Key generation
var key = Data(count: 32)
_ = key.withUnsafeMutableBytes {
    SecRandomCopyBytes(kSecRandomDefault, 32, $0.baseAddress!)
}

// Encryption
let sealedBox = try ChaChaPoly.seal(data, using: SymmetricKey(data: key))
```

**Properties:**

* 256-bit keys stored in iOS Keychain
* Poly1305 authentication tag
* Files written with `.completeFileProtection`
* Separate keys for Vault, Fade, and Backup vaults

---

## AES-256-GCM (Archives)

Password-derived encryption for archive files using Argon2id.

```swift
// Argon2id key derivation
let sodium = Sodium()
let salt = sodium.randomBytes.buf(length: 16)!

let key = sodium.pwHash.hash(
    outputLength: 32,
    passwd: password.bytes,
    salt: salt,
    opsLimit: sodium.pwHash.OpsLimitModerate,
    memLimit: sodium.pwHash.MemLimitModerate,
    alg: .argon2id13
)!

// AES encryption
let sealedBox = try AES.GCM.seal(data, using: SymmetricKey(data: key))
```

---

## Archive Modes

### Portable

ChaCha20 → Decrypt → AES-256-GCM (password-derived via Argon2id)

**Use Case:** Transfer vault to new device. Archive can be restored anywhere with the password.

### Secure

ChaCha20 → AES-256-GCM (device key)

**Use Case:** Encrypted backup that can only be restored on the same device. Double encryption for maximum protection.

---

## File Format

### .n11 (Photos)

[12-byte nonce][ciphertext][16-byte tag]

ChaCha20-Poly1305 encrypted JPEG/PNG/HEIC data.

### .n11note (Notes)

[12-byte nonce][ciphertext][16-byte tag]

ChaCha20-Poly1305 encrypted UTF-8 text.

### .nu11vlt (Archives)

ZIP Container:
├── manifest.json (encrypted)
├── Photos/
│   └── {ID}.n11 (encrypted)
└── Notes/
└── {ID}.n11note (encrypted)

Archive metadata and contents are AES-256-GCM encrypted.

---

## Argon2id Parameters

| Use Case          | opsLimit | memLimit |
| ----------------- | -------- | -------- |
| PIN Hashing       | 2        | 64 MB    |
| Archive Passwords | 3        | 256 MB   |

Fast enough for real-time PIN verification, resistant to GPU cracking for PINs. Stronger protection (~1s derivation) for archive passwords.

---

## Standards

* **ChaCha20-Poly1305:** IETF RFC 8439
* **AES-256-GCM:** NIST FIPS 197
* **Argon2id:** RFC 9106
* **Key Storage:** iOS Keychain (Secure Enclave)

---