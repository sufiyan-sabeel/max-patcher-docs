# Contributing

## Ground rules

1. **Preserve behaviour.** A change that removes a feature needs a written reason and agreement
   before the code.
2. **Evidence over assertion.** "It works" means an APK was patched, installed and used. Logs or it
   did not happen.
3. **No invented identifiers, no swallowed exceptions, no fake compatibility.**
4. **Attribution stays.** Upstream copyright headers and NOTICE text are not removable.

## Where things go

| Change | Repository |
|---|---|
| App UI, patching flow, installers | `max-patcher` |
| Patch DSL, fingerprints, dex/resource handling | `max-patcher/engine` |
| A patch or an extension | `max-patches` |
| Guides and the compatibility matrix | `max-patcher-docs` |

## Commits

Conventional commits (`feat:`, `fix:`, `docs:`, `build:`), and a body that says what you tested and
on which app version.

## Never commit

A signing key, a keystore, a token, `google-services.json`, or an APK. All are gitignored; if one
slips through, say so immediately rather than quietly deleting it.
