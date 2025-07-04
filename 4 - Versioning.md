# 📦 App Versioning Guide

This document outlines how and when to update the `versionCode` and `versionName` in your Android project's `build.gradle` to meet Google Play requirements and follow best practices.

---

## 🔢 Definitions

### `versionCode`
- A positive integer used internally by Google Play to identify app versions.
- **Must increase** with every release—Play Console rejects APK/AAB uploads with duplicate or lower codes :contentReference[oaicite:1]{index=1}.
- Doesn’t display to users; internally tracks build chronology.

### `versionName`
- A string meant for users, displayed in the Play Store and device settings :contentReference[oaicite:2]{index=2}.
- Commonly follows **Semantic Versioning**: `MAJOR.MINOR.PATCH` (e.g., `3.2.1`) :contentReference[oaicite:3]{index=3}.
- Optional to update for every release but recommended to reflect visible changes.

---

## 📅 When to Update

| Release Type        | versionCode | versionName          |
|---------------------|-------------|----------------------|
| **Every release**   | 👍 Increment by at least 1 :contentReference[oaicite:4]{index=4} | Optional: bump semver to indicate changes |
| **Patch/update**    | 👍 Increment | Recommended: bump PATCH (e.g., `3.2.0` → `3.2.1`) |
| **Minor feature**   | 👍 Increment | Recommended: bump MINOR (e.g., `3.2.1` → `3.3.0`) |
| **Major changes**   | 👍 Increment | Recommended: bump MAJOR (e.g., `3.3.0` → `4.0.0`) |

---

## 📈 Practical Example

Assuming your last released version was:

```groovy
versionCode 22
versionName "3.2"

