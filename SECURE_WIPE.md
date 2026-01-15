# Secure Wipe (NIST 800-88)

Optional single-pass overwrite to sanitize data before deletion.

## Primary Mechanism: Crypto-Shredding

Nu11VLT's primary method for data destruction is **crypto-shredding**. When content is deleted, the unique 256-bit `ChaCha20` key used to encrypt it is irrevocably destroyed from the Keychain.

On modern SSDs, this is the industry-standard method for secure data erasure. Without the key, the encrypted data is rendered useless ciphertext, making forensic recovery computationally infeasible.

## Optional Sanitization (Pro Feature)

For users who require an additional layer of forensic noise, Nu11VLT provides an optional **Secure Wipe** feature.

- **Standard:** Aligns with **NIST SP 800-88 Rev. 1** guidelines for media sanitization.
- **Process:** Performs a single pass of cryptographically secure random data across a file's logical address before it is deleted.
- **Use Case:** This adds a final layer of obfuscation, overwriting the ciphertext itself before the file is unlinked.