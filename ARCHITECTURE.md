# Architecture

Three artifacts, versioned independently:

| Artifact | Lives in | Version |
|---|---|---|
| MAX Patcher | `max-patcher/app` | 0.1.0 |
| MAX Engine | `max-patcher/engine` | 0.1.0 |
| MAX Patches | `max-patches` | 0.1.0 |

## The app

A single-module Android app plus one JVM module, wired with Koin:

- **UI** — Jetpack Compose, one folder per destination under `ui/screen/`, state in `ui/viewmodel/`.
- **Domain** — repositories and use cases under `domain/`: bundles, APKs, installers, workers,
  keystore, batch planning.
- **Persistence** — Room, with explicit migrations, for installed apps, original APKs, saved patched
  APKs, patch sources, options and selections.
- **Background work** — WorkManager for the patch queue and update checks; a foreground service for
  long patch runs.
- **Patching** — never in the UI process. `patcher-core` is packed into a `hostJar`, shipped beside an
  OpenJDK build for Android, and started in its own process. The app talks to it over three AIDL
  interfaces (`IPatcherService`, `JvmLaunch`, `HostStarted`), exchanging command and event messages.
  A 32-bit x86 device patches in-process instead, using Android's runtime.

The reason for the separate process is memory: patching a large APK needs a heap the app process
cannot have.

## The engine

A plain JVM library — no Android APIs — which is why it can be unit-tested and why it runs in either
runtime. It provides the patch DSL (`patch { }`, `dependsOn`, `compatibleWith`), the fingerprint and
instruction-filter machinery, dex read/write, resource table access through ARSCLib, APK merging,
signing and output validation.

It is consumed as a Gradle included build and substitutes the `app.morphe:morphe-patcher` coordinate,
so the app's source is unchanged and the package download disappears.

## The patches

Each patch is a Kotlin module declaring what it changes, which app versions it applies to, and its
dependencies. Many ship an extension: Java code injected into the target app. The patch and its
extension are two halves of one feature — the patch wires a hook, the extension decides what the hook
does.

Bundles are signed `.mpp` archives. MAX Patcher verifies a bundle against the public key the bundle
declares, falling back to a built-in default only when the bundle declares none.

## Data flow of one patch run

1. The app resolves the patch source, downloads and verifies the bundle.
2. The user picks an app and patches; the app checks the installed APK's signature against the
   declared compatibility.
3. Split APKs are merged and the original APK is stored for later re-patching.
4. `patcher-core` starts the embedded JVM, hands it the APK and the selected patches with their
   options, and streams progress back over AIDL.
5. The engine resolves dependencies, runs patches in order, writes the modified APK, signs it and
   returns it.
6. The app installs the result — session installer, root, or Shizuku — or exports it.
