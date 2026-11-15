# Installing MAX Patcher

## What you need

- An Android device, Android 8.0 (API 26) or newer. Patching YouTube needs Android 10 (API 29) or
  newer, because that is what the YouTube targets declare.
- The original app you want to patch, installed or available as an APK.
- Room for two copies of that app while patching.

## Install

There is no published release yet. Once there is one, it will be a signed APK attached to a GitHub
release, with a SHA-256 checksum beside it. Verify the checksum before installing:

```bash
sha256sum max-patcher-<version>.apk
```

MAX Patcher installs alongside the upstream manager: its application id is `app.maxpatcher.manager`,
so it will not replace or conflict with another manager.

## First run

1. **Add a patch source.** MAX Patcher ships with MAX Patches as its default source. If the source
   list is empty, add it from the repository URL.
2. **Pick an app.** MAX Patcher lists the apps it knows how to patch and detects which are installed.
3. **Pick patches.** Simple mode picks a sensible set; expert mode lets you choose each patch and its
   options.
4. **Patch and install.** Patching runs in an embedded JVM. The result is signed with a keystore MAX
   Patcher creates on first use, which is why the patched app cannot be updated over the original —
   Android sees a different signer.

## Keep your signing key

The key that signs patched apps is created on first patch. Back it up from Settings → System →
Signing key. Without it, future patched builds cannot be installed over the current one; you would
have to uninstall first, losing app data.

## What is not supported

- Patch sources that require a newer patcher than the engine you have. The source list says so
  instead of failing silently.
- Patching an app whose installed copy was modified by something else: the signature check refuses it.
