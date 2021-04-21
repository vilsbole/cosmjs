# Yarn 2 Migration Guide

Upgrading a monorepo from Yarn v1 with Lerna.js to Yarn v2 makes use of the weirdest versioning system I ever saw, and Yarn’s migration guide is not the most helpful: although it purports to guide you through the process in [a single document](https://yarnpkg.com/getting-started/migration), the information you need is actually spread across multiple pages in quite a confusing way, not helped by the poor English throughout.

This document is intended as a replacement for the official documentation (except where indicated), to help others get through the process faster than I did.

## 1. Update Yarn v1 to the latest version (eg v1.22)

For example: `npm install --global yarn` or `brew upgrade yarn`.

## 2. Enable Yarn v2 in your project

Navigate inside the project and run `yarn set version berry`.

From the [docs](https://yarnpkg.com/getting-started/install#per-project-install):

> "Berry" is the codename for the Yarn 2 release line. It's also the name of our repository!

This will create a `.yarn/` directory with a `yarn-berry.cjs` release inside and a`.yarnrc.yml`configuration file (a new format compared to the`.yarnrc` that was previously used).

## 3. Transfer your configuration to the new configuration file

You might not have had either, but any configuration in a `.npmrc` or `.yarnrc` file will need to be transferred to the newly created `.yarnrc.yml` file.

See [Update your configuration to the new settings](https://yarnpkg.com/getting-started/migration#update-your-configuration-to-the-new-settings) and [Configuration](https://yarnpkg.com/configuration/yarnrc).

## 4. Add any other basic configuration you need

Here’s what I used:

```yml
# Default is master instead of main
changesetBaseRefs:
  - "main"
  - "origin/main"
  - "upstream/main"

# For more info see https://yarnpkg.com/advanced/telemetry
enableTelemetry: false

# Experimental but we need this for the migration (we’ll switch it later)
nodeLinker: "node-modules"
```

## 5. Migrate the lockfile

Run `yarn install` and Yarn will sort it all out. It will probably give you a bunch of warnings.

## 6. Tell VCS which files to ignore

Add this to your `.gitignore` (or whatever):

```
.yarn/*
!.yarn/patches
!.yarn/releases
!.yarn/plugins
!.yarn/sdks
!.yarn/versions
.pnp.*
```

## 7. Commit files

Now is a good time to commit the new `.yarnrc.yml` and `.yarn/releases/yarn-berry.cjs` files to VCS, as well as the updated `yarn.lock` and `.gitignore` files.

## 8. Update commands and options

Some command and option names have changed from v1 to v2. For example `--frozen-lockfile` has been deprecated in favour of `--immutable`. Other option changes weren’t covered in the official documentation, so have fun finding out which ones still work. A table of changes to commands can be found [here](https://yarnpkg.com/getting-started/migration#renamed).

## 9. Adjust lifecycle scripts

Unlike npm or Yarn v1, Yarn v2 won’t run arbitrary lifecycle scripts (i.e. `prexxx` and `postxxx`). There are [some exceptions](https://yarnpkg.com/advanced/lifecycle-scripts), including `postinstall`, but if you’ve been relying on any other lifecycle scripts you will need to [explicitly call them](https://yarnpkg.com/getting-started/migration#explicitly-call-the-pre-and-post-scripts) in the main script definition.
