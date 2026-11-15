# Building from source

## The app

Requirements: JDK 17, 21 and 25; Android SDK platform 37; NDK and CMake; Node.js for the release
pipeline only.

```bash
git clone --recurse-submodules https://github.com/sufiyan-sabeel/max-patcher
cd max-patcher
git submodule update --init --recursive
./gradlew :app:assembleDebug
```

No GitHub token is needed. The patcher is built from `engine/` and its libraries from `vendor/`
submodules pinned to exact commits, so nothing is fetched from a package registry. This is the
substantive difference from the upstream build.

## The engine

```bash
./engine/gradlew -p engine test
./engine/gradlew -p engine apiCheck
```

`apiCheck` fails if the public API of the engine changed, which is what protects patch compatibility.

## The patches

`max-patches` applies the `app.morphe.patches` Gradle plugin, which is published only to the Morphe
GitHub Packages registry, so building it needs a personal access token with `read:packages`, stored
as the `GHP_PACKAGES_TOKEN` secret. Until that plugin is vendored, this is the one place a token is
required.

## Reproducibility

Pins are recorded in `vendor/PINS.md` in the app repository. CI fails if a submodule is not at its
pinned commit. Dependency verification metadata and dependency locking are **not** yet enabled, and
the version code is derived from the build timestamp, so two builds of one commit differ in
`versionCode`. That is documented rather than glossed over.
