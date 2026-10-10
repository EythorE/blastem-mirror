# blastem-mirror

Automated daily mirror of [blastem](https://www.retrodev.com/repos/blastem) (Mike Pavone's Sega Genesis emulator), converted from Mercurial to Git.

## How it works

- **`.github/workflows/mirror.yml`** runs every day at **08:00 UTC** (and on demand). It clones the upstream `hg` repo, converts history to git via [hg-fast-export](https://github.com/frej/fast-export), re-applies these workflow files on top, and force-pushes to `main`.
- **`.github/workflows/build.yml`** triggers on any pushed tag matching `v*`. It builds blastem on Ubuntu, packages the binary plus runtime assets into `blastem-<tag>-linux-x86_64.tar.gz`, and attaches it to a GitHub Release.

## Tagging a build

Builds are driven by `workflow_dispatch` — pick any commit in the mirrored history and the workflow tags it + publishes a release.

From the CLI:

```sh
gh workflow run build.yml \
  --repo EythorE/blastem-mirror \
  -f commit=<sha-or-ref> \
  -f tag=v$(date +%Y.%m.%d)
```

Or from the GitHub UI: **Actions → Build (Linux) → Run workflow**, fill in commit + tag.

The workflow checks out the commit, runs `make`, packages binary + assets, creates the tag at that commit, and publishes a release with the tarball.

## Notes

- The `main` branch is **force-pushed** on every sync. Don't commit your own changes there — they'll be overwritten. Edit workflows in this directory only; they're preserved across syncs.
- Mercurial tags from upstream are pushed too but don't start with `v`, so they won't trigger builds.
