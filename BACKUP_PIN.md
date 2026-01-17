# Backup PIN (Plausible Deniability)

The Backup PIN feature provides plausible deniability, not guaranteed safety.

---

## Purpose

Backup PIN allows a second, independent vault to be opened using a different PIN. Its purpose is to reduce risk in coerced unlock scenarios by revealing an alternative dataset.

---

## Guarantees

- Separate encryption keys
- Independent storage
- Identical interface
- No in-app indicators that multiple vaults exist

The application itself cannot cryptographically prove which vault is "real."

---

## Limitations

- Does not protect against physical violence
- Does not protect against OS-level compromise
- Advanced forensic analysis may detect the presence of multiple vault structures

Backup PIN is intended for opportunistic or moderate threat scenarios, not nation-state adversaries.

---

## Best Practices

- Populate the backup vault realistically
- Occasionally access it to avoid obvious usage patterns
- Use strong PINs for both vaults
- Never rely on it as sole protection in high-risk environments

---

## Security Boundaries Summary

### Nu11VLT is designed to:
- Protect stored data if the device is lost, stolen, or examined while locked
- Prevent forensic recovery after deletion
- Minimize exposure in coercive unlock scenarios
- Eliminate cloud and server attack surfaces

### Nu11VLT is not designed to:
- Defeat compromised operating systems
- Protect data currently visible on screen
- Withstand physical violence
- Replace operational security practices

---
