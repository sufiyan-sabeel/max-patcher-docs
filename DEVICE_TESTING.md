# Device testing: MAX Patcher 0.1.0 development build

**Status: not performed.** Nothing in this document has been run on a device. It is the checklist a
tester works through, written so that a pass means something specific rather than "it opened".

Target device: **Vivo V2521, Android 16, API 36, arm64-v8a.**

---

## Before you start

Record these once, because every result below is only meaningful against them:

| Field | Value |
|---|---|
| APK | `max-patcher-0.1.0-debug.apk` |
| SHA-256 | `92ec0910ab3d8931c3c954a5ce23cf37a5413e628587bc9f8c0e97731dfb7210` |
| Commit | `9370012b9fc482964e176e4c7c8eeb5c37cdc25f` |
| Device | Vivo V2521, Android 16 (API 36) |
| Install date | |

Verify the checksum before installing. If it differs from the value above, stop: the file is not the
build that was tested in CI.

## 1. Installation

- [ ] `adb install -r max-patcher-0.1.0-debug.apk` succeeds, **or** the system installer accepts it.
- [ ] The app appears in the launcher as **MAX Patcher** with the MAX monogram, not a Morphe mark.
- [ ] The package is `app.maxpatcher.manager` (debug builds carry `.debug`): Settings → Apps → MAX
      Patcher → App info shows the expected package.
- [ ] If any other manager is installed, both coexist; neither replaces the other.
- [ ] Installing a *second* copy over the first fails with a signature conflict, as it should
      (different signing key). Uninstalling first then installing succeeds.

## 2. First launch

- [ ] The splash screen shows the MAX mark and does not flash a blank or white frame.
- [ ] No crash, no "Firebase" error dialog, no ANR within the first 10 seconds.
- [ ] The onboarding/showcase appears once and can be dismissed.
- [ ] Cold start again: onboarding does not reappear.
- [ ] With no network, the app still opens and shows its cached state rather than a spinner forever.

## 3. Application navigation

- [ ] Every top-level destination opens: Home, Patcher, Sources, Settings.
- [ ] Settings subpages open and the back gesture returns to where you came from.
- [ ] Rotation, or a theme change, does not lose your place.
- [ ] No destination shows the old Morphe name anywhere.

## 4. AMOLED / dark appearance

- [ ] Dark theme: surfaces are graphite/near-black, text remains legible.
- [ ] The accent is the MAX cyan/teal, not Morphe blue.
- [ ] With an AMOLED system setting (if the ROM exposes one), backgrounds are true black where the
      app supports it.
- [ ] Light theme is still usable (the app ships both).
- [ ] A custom app icon and header can be generated from Settings, and the files it asks you to
      supply are named `max_*` (this was renamed; see the report).

## 5. Application detection

- [ ] The home screen lists installed apps that have patch sources (YouTube, YouTube Music, Reddit).
- [ ] An installed app shows as installed, with its version.
- [ ] An app that is *not* installed is offered the APK route instead.
- [ ] A freshly installed app appears without restarting the app.

## 6. APK selection

- [ ] Choosing a local APK via the system picker works.
- [ ] A split-APK set (if you have one) is accepted and merged, and the resulting base APK is what
      gets patched.
- [ ] An APK whose signature does not match the declared compatibility is refused, with a message
      naming the reason.

## 7. Patch source management

- [ ] MAX Patches is present as a source without you adding it.
- [ ] A source can be added from a repository URL by hand.
- [ ] An add-source link (`https://sufiyan-sabeel.github.io/max-patcher-docs/add-source.html?github=owner/repo`)
      opens the confirmation dialog **in the app**, naming the repository, and only adds it after you
      confirm.
- [ ] The same source added twice is refused as a duplicate.
- [ ] A link naming a repository that does not exist is rejected with a message, not a crash.
- [ ] A source can be disabled, re-enabled, refreshed and removed.
- [ ] A source whose bundle fails signature verification is refused, and the reason is shown.

## 8. Patch listing

- [ ] The patch list loads for the selected app and version.
- [ ] Patch names and descriptions render, with no `%s` or missing-resource artefacts.
- [ ] Patches that are unavailable for this version are shown as such, not hidden silently.
- [ ] The eight patches affected by the NavigationBarHookPatch defect are visible and marked
      unsupported on 21.45.82 — they must not appear as usable.

## 9. Patch configuration

- [ ] Simple mode selects a sensible default set.
- [ ] Expert mode lets you tick and untick individual patches.
- [ ] A patch with options opens its option sheet and the choice survives leaving the screen.
- [ ] Turning on a patch that requires a dependency pulls the dependency in, and says so.

## 10. Patch execution

- [ ] Patching an app completes and produces a result.
- [ ] A patch that is expected to fail (the NavigationBarHookPatch dependents on 21.45.82) fails
      **with a clear error naming the missing drawable**, and does not hang or silently "succeed".
- [ ] Cancelling mid-run leaves no corrupt output and the app remains usable.

## 11. Progress reporting

- [ ] Progress advances monotonically and reflects real work, not a timer.
- [ ] The current step is named (reading APK, applying patch X, signing…).
- [ ] Logs are readable and exportable from Settings.

## 12. APK export

- [ ] The patched APK can be exported to a chosen folder.
- [ ] The exported file opens (it is a valid zip and a valid APK).
- [ ] Exporting to a location the app cannot write fails with a message, not a crash.

## 13. APK signing

- [ ] The first patch creates a signing key, and Settings → System shows it.
- [ ] The patched APK is signed with that key: `apksigner verify --print-certs` on the exported file
      prints one signer.
- [ ] A backup of the key can be exported and re-imported.

## 14. Installation of the generated APK

- [ ] Session installer: the system installer opens and completes.
- [ ] If you use Shizuku or root, that path also completes.
- [ ] The patched app launches and the patch's feature is actually present.
- [ ] Updating over a previous patched build signed with the same key works without uninstalling.

## 15. Error handling

- [ ] Airplane mode during a source refresh: the app says so and keeps the previous state.
- [ ] Patching with insufficient storage: refused with a clear message before starting.
- [ ] A deliberately corrupt APK is refused with a message naming the problem.
- [ ] No stack trace is shown to the user; the debug log has it.

## 16. Notifications

- [ ] Notification permission is requested once, with a reason.
- [ ] Patching in the background shows a progress notification.
- [ ] Completion and failure each show a distinct notification.
- [ ] **Without** a Firebase config (this build): no push notifications arrive, and nothing crashes
      — that is the expected behaviour, not a bug.

## 17. Storage permissions

- [ ] On first use of a file picker, the app asks for what it needs and explains why.
- [ ] Denying the permission leaves the app usable, with a message rather than a dead end.
- [ ] Android 16 scoped storage: the app reads its own `/Android/data/app.maxpatcher.manager/files/`
      directory, which is what its instructions name.

## Reporting a result

For each item: **pass**, **fail** (what happened, what you expected), or **blocked** (why). Attach the
debug log for any failure. A checklist with items left unticked is a legitimate result; a checklist
with everything ticked but no device named is not.
