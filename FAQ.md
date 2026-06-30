# FAQ

## What happens if I lose my device?

Your data is unrecoverable unless you exported an encrypted archive. This is the cost of zero-knowledge storage.

---

## Can you access my photos?

No. There are no servers, accounts, or recovery systems. Keys never leave your device.

---

## Does the app use the internet?

No. Nu11VLT has no network functionality.

---

## What if I forget my PIN?

Your vault is permanently inaccessible. There is no recovery mechanism by design.

---

## Is FaceID safe?

FaceID uses the Secure Enclave. It improves convenience but can be coerced. High-risk users should prefer PIN-only unlock.

---

## Does the app remove GPS location data from my photos?

The in-app photo viewer never displays GPS coordinates — only camera details like make, model, ISO, and shutter speed. GPS data is **not** stripped from the original photo file itself, though; it's stored, encrypted, exactly as imported. If you need to guarantee a photo carries no location data at all, remove it before importing — the iOS Photos app supports this via "Remove Location" in its share sheet.

---

## What's inside an encrypted archive — can someone tell what I backed up without the password?

No. Archive exports are "sealed": the entire internal listing — file names, note titles, item counts, and dates — is encrypted, not just the photos and notes themselves. Without the correct password, an archive file reveals nothing about its contents. See [ARCHIVES.md](./ARCHIVES.md).

---

## What's the difference between Secure and Portable archive exports?

**Secure** archives stay tied to the device that created them — useful as an encrypted local backup. **Portable** archives can be restored on any device with the password — useful for moving to a new phone. Both get the same encrypted, sealed index; the difference is only in whether the content is also bound to your original device's key. See [ARCHIVES.md](./ARCHIVES.md).

---

## User Recommendations

- Keep iOS updated
- Never jailbreak your device
- Use longer PINs
- Enable auto-lock
- Use encrypted exports if data matters
- Treat Backup PIN as a risk-reduction tool, not a guarantee

---

## Final Note

Nu11VLT reduces digital risk. It cannot eliminate physical, legal, or operational risk. No consumer application can.

---
