# Skeleton Switch

Auto-wipe dead man's switch with a configurable inactivity timer.

## How It Works

Choose a check-in interval: 7, 14, 30, or 60 days. Every time you successfully unlock the app, your check-in timer resets. If the app isn't opened again before the interval elapses, the wipe runs automatically the next time it is launched.

## Wipe Modes

**Fast:** Standard key crypto-shredding & file deletion (near-instant)
**Secure:** Adds a supplementary overwrite pass before deletion (slower, more thorough — see [SECURE_WIPE.md](./SECURE_WIPE.md))

## Scope

Wipes Vault, Fade, and Backup photos and notes. All encryption keys are destroyed and storage is recreated empty.

## Limitations

- Triggers on next app launch, not instantly while the device is being held
- Survives app updates, not app deletion/reinstall
- Relies on the device's system clock — this is a convenience feature for opportunistic protection, not a hardened anti-tamper mechanism against a sophisticated adversary with extended device access
