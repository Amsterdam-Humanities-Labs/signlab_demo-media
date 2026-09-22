# signlab_demo-media
The video files of the SignCollect demo dataset: 292 files, 291 MB. That is 20 studio recordings with 3 or 5 camera angles each, in raw and post cuts. Every MP4 has a JPG thumbnail.

**These videos show research participants who can be identified.** The signers are visible, and each recording shows its record id as a QR code. Treat this repo like the studio archive it came from. It is private and must stay private.

## What it does
- It lets a demo host load demo data without access to production. The demo host is firewalled from production. This repo is not an archive and not the main copy of anything.
- The database rows and the videos are one dataset kept in two places. `db/demo-data.sql` names these files in `matched_transcriptions.l/m/r/a/b_file`.
- `db/demo-media.txt` lists what must be here: one line per angle with its cuts. `scripts/seed-demo-data.sh` checks every `.mp4` and `.jpg` and stops if one is missing. Add the files here first, then add the line there.

## Where it runs
Demo hosts only, as `<docroot>/gebarenoverleg_media` (dev2: `/web`, dev-1: `/srv/signcollect/web`). That is the same path as on production.
The core server does not get it, because it already has these files.

## Status
Production. A demo install fails with a clear error without it.

## How to run / deploy
Each demo host clones it through `interface_deploy/scripts/repos.tsv` (`gebarenoverleg_media  signlab_demo-media  main`) in [signlab_signcollect-stack](https://github.com/Amsterdam-Humanities-Labs/signlab_signcollect-stack).
`scripts/seed-demo-data.sh` then hard-links the post cuts into `/web/media_stub`. That folder stands in for `media.signcollect.nl`, whose docroot is `post/`. Nothing else touches these files.
Refilling this repo is a deliberate copy that someone reviews. It is never an install step.

Layout:
```
studioFilesMini/
  raw/<stem>.mp4     the camera cut
  raw/<stem>.jpg     its thumbnail
  post/<stem>.mp4    the post-processed cut
  post/<stem>.jpg    its thumbnail
```
Each angle has its own stem. For example `M20260227_5998`, `L20260227_7582` and `R20260227_0221` are one recording. Only the `matched_transcriptions` row links them. The A and B angles (13 of 20 recordings) have raw cuts only, as on production.

Adding files. Git keeps every file forever: a deleted file stays in every clone and on GitHub. Removing it needs a history rewrite, and old clones still have it.
- Add only recordings that may stay in a demo dataset for good. Studio consent is not consent to share.
- Add nothing else: no transcripts, exports, dumps, names, or anything that links a recording to a person.
- If a participant withdraws, purge the history and every clone. Until then, treat the repo as compromised.

## Configuration
None. No code, no build step, no submodules.
The repo does not use Git LFS. A plain `git clone` must work without `git-lfs` or an LFS quota, and Git cannot store video as small diffs anyway, so LFS saves nothing. Reconsider if the repo grows ten times larger.

## Dependencies
- [signlab_signcollect-stack](https://github.com/Amsterdam-Humanities-Labs/signlab_signcollect-stack) `interface_deploy/`: `repos.tsv`, `db/demo-media.txt`, `db/demo-data.sql`, `scripts/seed-demo-data.sh`.
- Readers: [signlab_signCollect-v2](https://github.com/Amsterdam-Humanities-Labs/signlab_signCollect-v2) (`js/main.js`, `js/table.js`), [signlab_zin](https://github.com/Amsterdam-Humanities-Labs/signlab_zin) (`getZinnen.php`), and [signlab_studioIndex](https://github.com/Amsterdam-Humanities-Labs/signlab_studioIndex). studioIndex asks for the thumbnail of every angle, which is why the A/B angles and the thumbnails are here.
