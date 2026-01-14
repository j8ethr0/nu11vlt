# DoD 5220.22-M Secure Deletion

3-pass overwrite implementation following U.S. Department of Defense standard.

## Process

```swift
func secureDelete(at path: String) throws {
    let fileHandle = try FileHandle(forWritingTo: URL(fileURLWithPath: path))
    let fileSize = try FileManager.default.attributesOfItem(atPath: path)[.size] as! UInt64
    
    // Pass 1: Zeros
    overwriteFile(fileHandle, size: fileSize, with: 0x00)
    fileHandle.synchronizeFile()
    
    // Pass 2: Ones
    overwriteFile(fileHandle, size: fileSize, with: 0xFF)
    fileHandle.synchronizeFile()
    
    // Pass 3: Random
    var randomData = Data(count: 65536)
    _ = randomData.withUnsafeMutableBytes { SecRandomCopyBytes(kSecRandomDefault, 65536, $0.baseAddress!) }
    overwriteFile(fileHandle, size: fileSize, withData: randomData)
    fileHandle.synchronizeFile()
    
    // Delete
    try FileManager.default.removeItem(atPath: path)
}
```

## Technical

- 64 KB chunks (memory efficient)
- `synchronizeFile()` after each pass
- Cryptographically secure random (Pass 3)
- Optional toggle in Settings

## SSD Limitations

Wear-leveling may leave data in unmapped NAND blocks. Combine with FileVault for maximum security.

## Standard

DoD 5220.22-M (NISPOM) - superseded by NIST SP 800-88 but remains effective
