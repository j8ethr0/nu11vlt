# Skeleton Switch

Auto-wipe dead man's switch with configurable inactivity timer.

## Configuration

Intervals: 7, 14, 30, or 60 days

```swift
// Timer stored in Keychain
let lastCheckIn = ISO8601DateFormatter().string(from: Date())
KeychainHelper.set(lastCheckIn, forKey: "lastCheckInTimestamp")

// Check on app launch
if let lastCheckInString = KeychainHelper.get("lastCheckInTimestamp"),
   let lastCheckIn = ISO8601DateFormatter().date(from: lastCheckInString) {
	let elapsed = Date().timeIntervalSince(lastCheckIn)
	if elapsed > configuredInterval {
		triggerWipe()
	}
}
```

## Wipe Modes

**Fast:** Standard file deletion (instant)  
**Secure:** DoD 5220.22-M 3-pass overwrite (slower, forensically secure)

## Scope

Wipes Vault, Fade, Decoy photos and notes. Directories recreated empty.

## Limitations

- Triggers on next app launch (not instant)
- Survives app updates, not deletion/reinstall
- Cannot be bypassed by changing system clock
