# Patch development

See also [COMPATIBILITY.md](COMPATIBILITY.md) for what "supported" means, and
[ARCHITECTURE.md](ARCHITECTURE.md) for how a patch and its extension fit together.

## A patch, minimally

```kotlin
val myPatch = patch(name = "My patch", description = "What it does for the user.") {
    dependsOn(sharedExtensionPatch, settingsPatch)
    compatibleWith(COMPATIBILITY_YOUTUBE)

    execute {
        // edits, or a call into the extension
    }
}
```

`dependsOn` is not advisory. If a dependency fails, the patch fails, and MAX Patcher reports it
against the patch the user selected.

## Fingerprints: the part that breaks

Obfuscation renames everything the compiler allows. A fingerprint that matched last month may match
nothing today, and the failure surfaces as "could not find" deep inside a patch the user did not
choose — because it was a dependency.

Rules:

- Match structure, not names: opcode sequences, method calls, casts, field accesses.
- If a resource name must be matched, decide whether a missing name is fatal. The default is fatal,
  and that default is what took out eight YouTube patches on 21.45.82. The alternative —
  `exceptionIfResourceNotFound = false` — turns a missing resource into a non-match, which is quieter
  but not automatically better: pair it with a version check and a second fingerprint.
- Gate by version. The engine exposes version flags (`is_21_43_or_greater` and friends) derived from
  the APK, and they are the intended mechanism.

## Extensions

Java, injected by class descriptor. Renaming an extension class breaks every patch that names it, so
those names are part of the compatibility surface, not an implementation detail.

## Testing

- Unit-test the logic that does not need an APK.
- Everything else needs a real APK of the exact version. Record what you ran in
  `COMPATIBILITY.md`, with the app version, patch set, engine version, device and result.
