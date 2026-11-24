# Sample library survey (aggregate only)

A first pass read a handful of local one-shot and loop folders: 302 files, with no read errors. Source files were not modified, moved or renamed. This note records only aggregate findings. File lists, paths, pack names and per-file manifests are not in this repository.

## Sample rates and formats

Of the 302 files:

- 289 (about 96%) were 44.1 kHz
- 9 were 96 kHz
- 4 were 48 kHz
- 296 were WAVE, 5 were AIFF, and 1 was AIFC
- 257 were 24-bit, and 45 were 16-bit
- 255 were stereo, and 47 were mono

Durations ranged from about 70 ms to 64 s (mean about 3.6 s). 137 files looked like one-shots, 150 like loops, and 15 were unclear.

## Classification

Folder and filename taxonomy was more useful than a features-only classifier.

- 210 of 302 files (69.5%) had some class cue in the name (instrument, loop, and so on)
- 142 (47%) had an instrument-class cue
- On those 142, a features-only classifier agreed exactly 40.1% of the time, and 44.4% of the time at a looser family level (for example treating related drum classes together)

Folder taxonomy should lead, with acoustic features as a support signal, not the other way around.

## Groove timing

Three loops were used as a timing trial. Raw onset-to-grid deviations were systematically late, which is consistent with librosa's onset detector lag. After removing that constant lag (the median signed deviation on each loop), the median absolute residuals were about 4 ms, 6 ms and 12 ms.

## Library completeness

Many packs appear only partly downloaded. In one commercial library root, 149 packs were listed: 138 contained audio, 82 of those had only 1–5 audio files, and 11 were empty. In another root, 278 packs all contained audio, but 207 of them had only 1–5 audio files.
