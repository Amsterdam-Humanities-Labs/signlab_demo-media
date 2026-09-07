# signlab_demo-media

The video half of the SignCollect demo dataset: 292 files, 291MB. Twenty studio
takes, each filmed from three or five camera angles, each angle held as a raw
cut and - where production made one - a post-processed cut, and each cut as an
MP4 with a JPG thumbnail beside it.

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
      raw/<stem>.jpg     its thumbnail
      post/<stem>.mp4    the post-processed cut
      post/<stem>.jpg    its thumbnail

An angle is a file of its own with a numeric suffix of its own: M20260227_5998,
L20260227_7582 and R20260227_0221 are the same recording from three cameras.
The only place that mapping exists is the matched_transcriptions row, which
names each angle in its own column (l_file, m_file, r_file, a_file, b_file), so
it can never be guessed from a filename. A and B were filmed on thirteen of the
twenty takes and post-processed on none of them, which is why those thirteen
have raw files only - production is the same, and the interface falls back to
the raw thumbnail by design.

The paths are deliberately identical to production's, under
`/web/gebarenoverleg_media/`. `signcollect-demovps/scripts/repos.tsv` maps this
repository onto `/web/gebarenoverleg_media`, so a plain `rsync` of the checkout
lands every file exactly where the interface looks for it. All 86 angles are listed in
`signcollect-demovps/db/demo-media.txt` with the cuts each one has, and that
list and this tree have to agree - the seed checks every line against the
checkout, both extensions, and refuses to run if one is missing.

The post cuts, thumbnails included, are additionally hard-linked into
`/web/media_stub` by `scripts/seed-demo-data.sh`, because the `/media` alias on
the demo host stands in for `media.signcollect.nl`, whose document root on
production is that same `post/` directory.

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
through a separate quota'd endpoint that fails differently from git. 291MB is
a slow clone, not a broken one, and it is a one-time cost per machine. The
video does not delta or repack, so it is 291MB once and forever either way -
LFS moves where the bytes are stored without making them any less permanent,
and it would put a quota between a fresh checkout and a working demo, which is
the property this whole arrangement exists to protect. Revisit it if this ever
grows by another order of magnitude.
