# signlab_demo-media
The video half of the SignCollect demo dataset: 292 files, 291 MB (20 studio takes, 3 or 5 angles, raw + post cuts, each MP4 with a JPG thumbnail).

**This is video of identifiable research participants.** Signers are visible throughout and each take has its record id filmed in as a QR code. Treat it like the studio archive it came from. It is private and stays private.

## What it does
- Lets a demo host be seeded without reaching production (the demo is firewalled from it). Not an archive, not the canonical copy of anything.
- Rows and video are one dataset in two places: `db/demo-data.sql` (`matched_transcriptions.l/m/r/a/b_file`) names these files.
- `db/demo-media.txt` is the contract: one line per angle with its cuts. `scripts/seed-demo-data.sh` checks every `.mp4` and `.jpg` and refuses to run if one is missing. **Add files here first, then the line there.**

## Where it runs
Demo hosts only, as `<docroot>/gebarenoverleg_media` (dev2: `/web`, dev-1: `/srv/signcollect/web`), the same path as production.
Not deployed to the core server: production already has these files.

## Status
Production (a demo install fails loudly without it).

## How to run / deploy
Cloned by the host itself via `interface_deploy/scripts/repos.tsv` (`gebarenoverleg_media  signlab_demo-media  main`) in [signlab_signcollect-stack](https://github.com/Amsterdam-Humanities-Labs/signlab_signcollect-stack).
`scripts/seed-demo-data.sh` then hard-links the post cuts into `/web/media_stub` (the demo's stand-in for `media.signcollect.nl`, whose docroot is `post/`). Nothing else touches these files.
Re-filling this repo is a deliberate, reviewed copy, never an install step.

Layout:

    studioFilesMini/
      raw/<stem>.mp4     the camera cut
      raw/<stem>.jpg     its thumbnail
      post/<stem>.mp4    the post-processed cut
      post/<stem>.jpg    its thumbnail

Each angle has its own stem (M20260227_5998, L20260227_7582 and R20260227_0221 are one recording); only the `matched_transcriptions` row maps them. A/B angles (13 of 20 takes) have raw cuts only, as on production.

## Configuration
None. No code, no build, no submodules.

**History is permanent.** A removed file stays in every clone and in GitHub's storage; purging needs a history rewrite and still leaves old clones.
- Only add a recording that may permanently be part of a demo dataset (studio consent is not consent to redistribution).
- Add nothing else: no transcripts, exports, dumps, names, or anything pairing a recording with a person.
- A participant withdrawal means purging history *and* every clone; treat the repo as compromised until done.

Not Git LFS: plain `git clone` must work without `git-lfs` or an LFS quota, and video does not delta, so LFS saves nothing. Revisit if it grows 10x.

## Dependencies
- `signlab_signcollect-stack` `interface_deploy/`: `repos.tsv`, `db/demo-media.txt`, `db/demo-data.sql`, `scripts/seed-demo-data.sh`.
- Readers: `signlab_signCollect-v2` (`js/main.js`, `js/table.js`), `signlab_zin` (`getZinnen.php`), `signlab_studioIndex` (requests every angle's thumbnail, hence A/B and thumbnails are here).
