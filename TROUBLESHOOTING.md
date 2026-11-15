# Troubleshooting

## "Could not find resource type: DRAWABLE name: …"

A patch's fingerprint referenced a resource the APK does not ship, and the engine treats that as
fatal by default. On YouTube 21.45.82 this happens with `yt_fill_bell_black_24` and takes out eight
patches. It is a known defect; see [COMPATIBILITY.md](COMPATIBILITY.md). Use a version that does not
hit it, or wait for the fix.

## "X depends on Y" and the patch is skipped

Y failed, so X could not run. Read the earlier error: the real failure is Y's. This is correct
behaviour, not a second bug.

## The patched app will not install: signature conflict

Android will not install a build signed with a different key over an existing one. Uninstall the
existing app first — which loses its data — or install the patched build alongside under a different
package name, if the patch source supports that.

## Patching fails on a large APK

Patching runs in an embedded JVM with its own heap. Close other apps, and make sure there is free
storage for at least two copies of the APK.

## "This source requires patcher X or newer"

The patch bundle was built for a newer engine than you have. Update MAX Patcher, or pick an older
bundle from the source's releases.

## Patch source will not load

Check its URL and that it publishes a `patches-bundle.json`. If the source declares a signing key,
the bundle must match it; a bundle signed by a different key is refused by design.

## Update notifications never arrive

If the build has no Firebase configuration, push notifications are inactive by design and update
checks fall back to periodic background checks. See the Firebase section of the app's
`docs/FIREBASE.md`.

## Nothing here helps

Open an issue with: app version, Android version, device, the patch source and bundle version, the
patch set, and the exported debug log from Settings. Without those, a report cannot be acted on.
