# signlab_demo-media

The video half of the SignCollect demo dataset: 40 MP4 files, 68MB, the raw and
the post-processed cut of each of 20 studio takes.

**This is video of identifiable research participants.** Their faces are the
content - these are sign language recordings, so the signer is visible
throughout, and every take carries a QR code with its record id filmed into the
head of it. Treat this repository the way you would treat the studio archive it
came out of. It is private, and it stays private.

## What it is for

`signcollect-demovps` stands a demo of the SignCollect interface up on a fresh
VPS. Everything it needs comes from GitHub: the component repos, the vendored
Apache config, the schema and the demo rows. This repository is the one
remaining piece - without it the demo comes up with empty video players, and
the only other place to get these files is the production server, which the
demo host is deliberately firewalled away from.

So: this exists so a demo can be seeded without production being reachable.
That is its whole purpose. It is not an archive, not a distribution channel,
and not the canonical copy of anything.

## Layout

    studioFilesMini/
      raw/<stem>.mp4     the camera cut
      post/<stem>.mp4    the post-processed cut

The paths are deliberately identical to production's, under
`/web/gebarenoverleg_media/`. `signcollect-demovps/scripts/repos.tsv` maps this
repository onto `/web/gebarenoverleg_media`, so a plain `rsync` of the checkout
lands every file exactly where the interface looks for it. The 20 stems are
listed in `signcollect-demovps/db/demo-media.txt`, which is what the SQL seed
and this tree have to agree on.

The post cuts are additionally hard-linked into `/web/media_stub` by
`scripts/seed-demo-data.sh`, because the `/media` alias on the demo host stands
in for `media.signcollect.nl`, whose document root on production is that same
`post/` directory.

## Git history is permanent

Anything committed here cannot be withdrawn. A commit can be reverted, a branch
can be deleted, a file can be removed from the tip of `main` - and the object is
still in the pack, still fetched by the next `git clone`, still on every machine
that has ever cloned this. Removing it for real means rewriting history and
force-pushing, and even then it survives in every existing clone and in
GitHub's own unreachable-object storage for some time.

Consequences, in the order they matter:

1. **Do not add a recording here unless it may permanently be part of a demo
   dataset.** Consent to being recorded in the studio is not consent to being
   redistributed in a checkout that gets cloned onto laptops and demo servers.
2. **Do not add anything else at all.** No transcripts, no exports, no database
   dumps, no participant names, no file that pairs a recording with a person.
   The MP4s are the only content this repository is for.
3. If a participant withdraws, the honest answer is that the file has to be
   purged from history *and* from every clone, and this repository has to be
   treated as compromised until that is done. Plan for that being expensive.

## Why not Git LFS

Plain `git clone` has to keep working for anyone deploying the demo, on a
machine that may not have `git-lfs` installed, and LFS objects are fetched
through a separate quota'd endpoint that fails differently from git. 68MB of
already-compressed video is a size a normal clone handles fine. It does not
delta or repack, so it is 68MB once and forever - which is the real cost here,
and it is the same cost with LFS. Not worth the extra moving part.
