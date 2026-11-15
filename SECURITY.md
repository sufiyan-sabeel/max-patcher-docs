# Security

## Reporting a vulnerability

Open a private security advisory on the affected repository, or email the maintainer listed on the
GitHub profile. Do not open a public issue for anything that could be exploited before it is fixed.

Include: what is affected, how to reproduce it, and what an attacker gains.

## What this project will never ask for

- Your GitHub personal access token. A token belongs in GitHub's secret store or your own
  `~/.gradle/gradle.properties`, nowhere else.
- Your signing keystore or its passwords.
- Your Firebase service account key.

Anyone asking for those in an issue thread, a chat or a "support" message is not acting for this
project.

## How secrets are handled

| Secret | Where it lives |
|---|---|
| `GHP_PACKAGES_TOKEN` | GitHub Actions secret, written to `~/.gradle/gradle.properties` in CI |
| Release keystore | `KEYSTORE_B64` secret, decoded only inside the release job |
| Patch bundle signing key | CI secret; only the public half is published |
| `google-services.json` | gitignored, supplied as a CI secret or kept locally |

No secret is committed, echoed into a log, or passed on a command line where it would appear in
process listings.

## Scope

Patch sources are third-party code by design. MAX Patcher verifies bundle signatures where a source
declares a key, and refuses bundles that do not match, but it cannot audit what a patch does to the
app it patches. Treat unknown patch sources as you would unknown software.
