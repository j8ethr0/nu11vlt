# Secure Wipe

Crypto-shredding is the primary, always-on mechanism Nu11VLT uses to destroy data. An optional single-pass overwrite adds a further layer of obfuscation on top.

## Primary Mechanism: Crypto-Shredding

Nu11VLT's primary method for data destruction is **crypto-shredding**. When content is deleted, the unique 256-bit ChaCha20 key used to encrypt it is irrevocably destroyed from the Keychain.

Without the key, the encrypted data left behind is permanently unreadable ciphertext — recovering the original content is computationally infeasible, regardless of what remains on the physical storage. This is the data-destruction approach **NIST SP 800-88 Rev. 1** recommends for flash-based storage (the type used in iPhones), since a logical-level overwrite isn't guaranteed to reach every physical location a deleted file's data once occupied, due to wear-leveling on flash storage controllers.

## Optional Sanitization (Pro Feature)

For users who want an additional layer of forensic noise on top of crypto-shredding, Nu11VLT provides an optional **Secure Wipe** feature.

- **Process:** Performs a single pass of cryptographically secure random data over a file's storage location before it is deleted.
- **Use case:** Overwrites the ciphertext itself before the file is unlinked, as a supplementary precaution beyond crypto-shredding.
- **Note:** On flash storage, an overwrite pass is not independently verifiable the way crypto-shredding is — treat it as extra obfuscation, not a separate guarantee.
