# Threat Model & Security Overview

This document is intentionally high-level. It explains what Nu11VLT is designed to protect against, what it is not designed to protect against, and how features such as Backup PIN fit into that model — without exposing implementation-sensitive details.

---

## Threat Model

### Design Goals

Nu11VLT is designed to protect sensitive personal photos and notes stored locally on iOS devices against:
- Device loss or theft
- Forensic data recovery after deletion
- Cloud compromise
- Opportunistic or moderate forensic analysis
- Coerced unlock scenarios (plausible deniability)

It is **not** designed to defeat operating-system compromise, nation-state zero-days, or physical violence.

---

## Protected Against

### 1. Physical Device Seizure or Theft

**Scenario:** Phone is stolen, confiscated, or examined while locked.

**Protections:**
- All data encrypted at rest using modern AEAD encryption
- Hardware-backed key storage (iOS Secure Enclave)
- No plaintext thumbnails or metadata
- App data excluded from cloud backup

**Result:** Without successful authentication, stored data is computationally infeasible to recover.

---

### 2. Cloud or Server Breaches

**Scenario:** iCloud, third-party services, or developer infrastructure compromised.

**Protections:**
- No accounts, no servers, no sync
- No network permissions
- All processing is on-device

**Result:** There is no remote attack surface and no off-device data to breach.

---

### 3. Forensic Recovery After Deletion

**Scenario:** Investigator attempts to recover deleted photos using forensic tools.

**Protections:**
- Crypto-shredding through encryption-key destruction
- Optional secure overwrite before deletion (Pro)

**Result:** Deleted content remains encrypted noise even if raw storage blocks are recovered.

---

### 4. Extended Detention / Device Seizure

**Scenario:** Device is held for long periods while user cannot access the app.

**Protections:**
- Optional Skeleton Switch (dead-man's switch)
- Automatic key destruction after configurable inactivity window

**Result:** If the device is retained long enough, protected data self-destructs on next launch.

---

### 5. Coerced Unlock (Plausible Deniability)

**Scenario:** User is forced to unlock the app at a checkpoint or under duress.

**Protections:**
- Backup PIN opens a separate alternative vault
- Identical interface
- Independent encryption keys

**Result:** An alternative dataset can be revealed without exposing the primary vault.

---

### 6. Offline Brute-Force Attempts

**Scenario:** Attacker extracts app storage and attempts to crack PINs or archives.

**Protections:**
- Memory-hard password hashing
- Secure Enclave-protected keys
- iOS rate limiting

**Result:** Offline cracking becomes computationally expensive and impractical at scale.

---

## Not Protected Against

### 1. Device Access While App Is Unlocked

Decrypted data exists in memory while the vault is open. Anyone with immediate access can view or capture it.

---

### 2. iOS or Secure Enclave Compromise

Kernel exploits, Secure Enclave vulnerabilities, or malicious firmware could bypass any app-level protections.

---

### 3. Jailbroken Devices

Jailbreaking breaks iOS's trust model. Nu11VLT is not safe to use on jailbroken devices.

---

### 4. Physical Violence or Extreme Coercion

No cryptographic system can protect against violence or threats to life.

---

### 5. Weak User Passwords

Very weak PINs or archive passwords reduce protection regardless of encryption strength.

---

## Assumptions

- iOS security model is intact
- Device is not jailbroken
- User keeps iOS up to date
- PINs and archive passwords are non-trivial

---
