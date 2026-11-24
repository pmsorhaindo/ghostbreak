# Ghostbreak

Ghostbreak generates fresh drum breaks as Elektron Octatrack projects, as a rhythmic basis for tracks.

This repository is research only. There is no code yet.

## What it is meant to do

1. Find one-shot samples in a user's library.
2. Classify them (kick, snare, hi-hat, vocal, perc, synth, and so on).
3. Optionally process none, some or all of the hits. Processing includes 44.1 kHz band-limited resample copies, with dither when bit depth drops, and leading-silence padding where Octatrack micro-timing cannot place a hit exactly.
4. Sequence them Octatrack-style, with parameter locks (p-locks) and micro-timing.
5. Write a full Octatrack project, with samples assigned to the correct tracks per pattern.
6. Rate the patterns so the system can learn and generate the next set to match.
7. Learn groove timing, pitch changes and similar traits from existing breaks.

## Rules

- Never modify, move or delete source samples. Work from copies only.
- Convert everything to 44.1 kHz. Conversions are copies only, using band-limited resampling, with dither when bit depth drops.
- The Octatrack writer will be a clean-room MIT implementation. No GPL or AGPL dependencies, and no bundled Elektron or octapy factory templates.
- Hardware testing only on a spare CompactFlash (CF) card or a separate set, never on a live working card.
- The ot-tools / ot-tools-io author's policy forbids AI tools scanning that source. Only its published docs.rs documentation may be used.
- Commercial break recordings are used only privately. They are never committed here and never used to train anything public.
- Product names are "inspired by" only. This project makes no brand claims.

## Workflow

Implementer: Grok Build on Grok 4.7. Reviewer: Google Neon / Antigravity. The implementer and reviewer come from different model families.

## Status

Research notes are in [`docs/research/`](docs/research/). No implementation work has started.

Open questions before any code is written:

- Whose Octatrack, and which CompactFlash card, will we test on (a spare card, or a separate set)?
- Where will pattern ratings happen?

## Licence

MIT. Copyright (c) 2026 Michael Sorhaindo.
