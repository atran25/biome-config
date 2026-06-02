---
name: publish-npm-version
description: Guide Codex through publishing a new npm package version for this repository. Use when the user asks how to release, publish, bump, tag, or ship a new npm version of the biome-config package, especially with Git tags, GitHub Actions, npm Trusted Publishing, npm version, npm pack --dry-run, or npm publish.
---

# Publish npm Version

## Overview

Use this skill to help publish a new version of the `@atran25/biome-config` npm package from this repository. Prefer the tag-driven GitHub Actions flow because the package is configured for npm Trusted Publishing.

## Release Checklist

1. Inspect the current state:

```sh
git status
npm pkg get version
```

2. Confirm the package contents before changing the version:

```sh
npm pack --dry-run
```

Verify that the output includes `package.json` and `biome.json`, and does not include `node_modules`.

3. Choose the version bump:

```txt
patch: small fixes or metadata-only updates
minor: new config behavior that should be compatible
major: breaking or very noisy config changes
```

4. Create the version commit and tag:

```sh
npm version patch
```

Replace `patch` with `minor` or `major` when appropriate. This updates `package.json`, updates `package-lock.json` when present, creates a Git commit, and creates a tag such as `v1.0.1`.

5. Push the commit and tag:

```sh
git push origin main --follow-tags
```

6. Watch the GitHub Actions publish workflow. It should run on the new `v*` tag and publish via npm Trusted Publishing.

7. Verify the npm package after the workflow succeeds:

```sh
npm view @atran25/biome-config version
npm view @atran25/biome-config versions --json
```

## Important Guardrails

- Do not run `npm publish` locally unless the user explicitly wants a manual fallback.
- Do not use an npm API token for the GitHub Actions flow. Trusted Publishing should use GitHub OIDC with `id-token: write`.
- Do not ignore `package-lock.json`; commit it so `npm ci` works in GitHub Actions.
- If `npm version patch` fails because the working tree is dirty, inspect the changes and ask whether they should be committed before releasing.
- If the GitHub Action fails because the version already exists on npm, bump to a new version; npm package versions are immutable once published.

## Project-Specific Details

- Package name: `@atran25/biome-config`
- Shared config export: `@atran25/biome-config/biome`
- Expected config file: `biome.json`
- Recommended workflow trigger: `push` tags matching `v*`
- Expected publish command in CI:

```sh
npm publish --access public
```

## Manual Fallback

If GitHub Actions or Trusted Publishing is unavailable and the user wants to publish manually:

```sh
npm pack --dry-run
npm publish --access public
```

Only use this fallback after confirming the user is logged into the correct npm account with:

```sh
npm whoami
```
