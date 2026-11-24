# Octatrack set/project formats — provenance-tracked spec for Ghostbreak

Rewritten 24 Nov 2026, 17:26 CET (Europe/Amsterdam), after local research clones of ot-tools, ot-tools-io and a related fork were deleted. Those repositories' CONTRIBUTING forbids AI "scanning the repository for information" and only allows AI use on the published library docs. This file therefore cites **only allowed sources**. It does not cite ot-tools source, and it does not treat the earlier 907-bank count (which came from ot-tools-io `test-data`, now deleted) as evidence.

**Allowed sources used here**

- Elektron *Octatrack MKII User Manual*, OS 1.40C (official PDF, 26 Aug 2026): `https://www.elektron.se/wp-content/uploads/2026/08/Octatrack-MKII-User-Manual_ENG_OS1.40C_260827.pdf` (a local extract of the official PDF was used for page cites).
- Rendered docs.rs pages for `ot-tools-io` 0.11.3 — **API/docs pages only, never `/src/` views**, especially `https://docs.rs/ot-tools-io/latest/ot_tools_io/` and `…/banks/struct.BankFile.html`, `…/markers/struct.MarkersFile.html`, `…/patterns/struct.Pattern.html`, `…/trait.HasChecksumField.html`.
- Remaining local clones (read for understanding, not to copy): octapy, OctaChainer / OctaChainer2, OctaLib, DigiChain, takoyaki, Octatrack Manager, octarranger, OctaDoc, ot_utils, OSSC/`octatrack-exporter`.
- OSSC `docs/notes-formats.md` and `src/lib/bankFile.js`.
- Elektronauts threads previously identified (`.work` / `.strd` user descriptions).
- Empirical checks on fixtures that **remain** in allowed repos, plus the factory project bundled in octapy (see below). **Do not ship that factory zip.**

**Empirical corpus actually used (after the deletion)**

- `octapy/octapy/templates/project-template-1.40B.zip` — octapy describes this as an on-device factory 1.40B project captured byte-for-byte. It contains the 26 `.work` files of a new project (16 banks, 8 arrangements, `markers.work`, `project.work`). Cite as “octapy factory template”. Do not copy it into a public MIT repo.
- Octatrack Manager fixtures: `src-tauri/tests/fixtures/real_device/` (`project.work`, `bank01.work`, `bank01.strd`, `markers.work`, `arr01.work`), `lfo_modes/bank01.work`, `multipart/bank01.work`, and `src-tauri/src/templates/blank_arrangement.work`.
- OctaDoc `const-editor/data/` (`bank01.strd`, `bank04.work`; `bank13.strd` is a 636,049-byte **version 22** file, useful only as a checksum check).
- takoyaki's `tests/fixtures/` banks / markers / arr / `.ot` are **mocks** (21 or 124 bytes, or a non-`FORM….DPS1SMPA` `.ot`) and were **not** used as ground truth.

The earlier “907 banks / 0x96BB blank checksum / 398+342 sample locks at +31” figures came from ot-tools-io test data and are **withdrawn**.

---

## 1. Set / project folder layout

**Cleanly sourced.** The official manual (p. 27–28) says a *set* contains an `AUDIO` folder (the audio pool) plus the set's projects; after opening a set folder, “the AUDIO folder and the saved projects of the set are visible.” A *project* contains 16 banks, 8 arrangements, 128 Flex slots, 128 Static slots, 8 recorder buffers, a BPM, and PROJECT-menu settings. Audio may also be placed in the project folder, but Elektron recommends putting samples in `AUDIO`. Flex and Static machines take **16- or 24-bit, 44.1 kHz WAV or AIFF**, mono or stereo (manual p. 27, §7.2.2).

On the card, that maps to:

```
<SET>/AUDIO/…
<SET>/<PROJECT>/project.work
                bank01.work … bank16.work
                arr01.work … arr08.work
                markers.work
```

The octapy factory template contains exactly those 26 `.work` files and no `.strd`. Octatrack Manager's `real_device` fixture is a saved project: it has `.work` files and also `bank01.strd`.

**PATH rules (cleanly sourced, with one caution).** `project.work` `PATH=` values in Manager `real_device` and in OSSC notes are relative to the **project folder**, with forward slashes; pool samples use `../AUDIO/…`. OSSC: slots are one-indexed; Flex recorders are 129–136. Never write backslashes — all allowed-repo device examples use `/`. A previous ot-tools source comment about backslash “catastrophic” corruption has been removed and is not re-cited; treat the exact failure mode as **UNSOURCED, re-derive** if we ever need the wording (hardware: write a `\` path and observe).

## 2. `.work` vs `.strd`

**Cleanly sourced** from the rendered docs.rs crate page, “Octatrack Device File Modifications” (`https://docs.rs/ot-tools-io/latest/ot_tools_io/`):

- `*.work` files are created by PROJECT → CHANGE PROJECT → CREATE NEW.
- `*.work` files are updated by PROJECT → SYNC TO CARD and by PROJECT → SAVE PROJECT.
- `*.strd` files are created/updated by PROJECT → SAVE PROJECT.
- `*.strd` files are **not** changed by SYNC TO CARD.
- `arr??.strd` can also be saved from ARRANGER → SAVE ARRANGEMENT.

This matches the octapy factory template (`.work` only) and Manager's open path, which tries `project.work` / `bankNN.work` first and falls back to `.strd` (`project_reader.rs:4098–4108`, `:5680–5686`). Elektronauts users describe `.strd` as the post-SAVE snapshot.

**What Ghostbreak must write for a new project:** the 26 `.work` files. Do not ship `.strd` in v1 (same as CREATE NEW). The user should SAVE PROJECT once on the device. If we ever edit an existing project, write `.work` only and leave `.strd` as the user's last save.

**Separate internal split (cleanly sourced, do not confuse with file extensions).** The rendered `BankFile` docs expose `parts_unsaved` and `parts_saved`. Manager documents that it writes the working copy and leaves the saved copy for the device's Reload Part (`project_reader.rs:4115–4120`). For a new project, writing the same part data into both copies is the conservative choice (so Reload Part matches the generated state); confirm on hardware.

## 3. `project.work` text format

**Cleanly sourced** from the octapy factory template and Manager `real_device/project.work`, with structure also summarised in OSSC `notes-formats.md` §project.work.

- CRLF line endings in both files.
- Sections: `[META]`, `[SETTINGS]`, `[STATES]`, then one or more `[SAMPLE]…[/SAMPLE]` blocks.
- Factory `[META]`: `TYPE=OCTATRACK DPS-1 PROJECT`, `VERSION=19`, `OS_VERSION=R0177     1.40B`.
- `[SETTINGS]` includes `TEMPOx24` (2880 = 120 BPM in the factory file), MIDI, metronome, recorder-reservation keys, etc.
- `[SAMPLE]` keys observed: `TYPE` (`FLEX` / `STATIC`), `SLOT` (1-based), `PATH`, `BPMx24` (factory), `TSMODE`, `LOOPMODE`, `GAIN`, `TRIGQUANTIZATION`. Factory recorders: `TYPE=FLEX`, `SLOT=129…136`, empty `PATH`, `TSMODE=2`, `GAIN=72`, `TRIGQUANTIZATION=-1`. Manager `real_device` has 34 `[SAMPLE]` blocks, 26 with `PATH=../AUDIO/…` (spaces in names are legal).

**Minimum valid file.** The factory template is only 2,899 bytes and lists **only the eight recorder slots**, not 128+128 empty Flex/Static slots. OSSC notes “Slots only stored once assigned.” The earlier “always emit 136 `[SAMPLE]` blocks” claim came from ot-tools blank fixtures and is **withdrawn**. **Recommendation:** emit `[META]`/`[SETTINGS]`/`[STATES]` plus a `[SAMPLE]` block for every slot we actually assign, and keep the eight recorder defaults as in the factory template. Whether the machine accepts that exact shape is already suggested by the factory capture, but still **hardware-test** a Ghostbreak-written file. Copy `OS_VERSION` from a blank the user creates on their own OT, not from a shipped blob.

`project.work` has no checksum (OSSC).

## 4. `bankNN.work` size, header, version

**Cleanly sourced**, independently, from:

- octapy `octapy/_io/bank.py`: `BANK_FILE_SIZE = 636113`, 21-byte `BANK_HEADER` `FORM\0\0\0\0DPS1BANK\0\0\0\0\0`, `BANK_FILE_VERSION = 23`, checksum at `0x9B4CF`.
- Rendered `BankFile` docs: 21-byte `header`, `datatype_version`, `patterns`, `parts_unsaved`, `parts_saved`, `parts_saved_state`, `parts_edited_bitmask`, `part_names`, `checksum`.
- OSSC `notes-formats.md`.
- Empirical: every real 636,113-byte bank in the remaining corpus (octapy factory ×16 identical banks, Manager `real_device` work+strd, `lfo_modes`, `multipart`, OctaDoc `bank01.strd` and `bank04.work`) has that header and version **23**. OctaDoc `bank13.strd` is 636,049 bytes / version **22** (older OS) — do not emit that size/version.

## 5. Pattern / part sizes and offsets

**Cleanly sourced** from octapy constants + empirical spacing in the factory bank (and the Manager banks):

| Item | Value | How we know |
| --- | ---: | --- |
| Patterns start | offset 22 | octapy `BankOffset.PATTERNS`; first `PTRN\0\0\0\0` there |
| Pattern size | 36,588 (`0x8EEC`) | octapy; 16 `PTRN` headers at `22 + p×36588` in the factory bank |
| Audio tracks start | pattern+8 | octapy `PatternOffset.AUDIO_TRACKS` |
| Audio track size | 2,338 (`0x922`) | octapy; 8 `TRAC` headers at `pattern+8+t×2338` (128/128 in the factory bank) |
| MIDI tracks start | pattern+18,712 | octapy `MIDI_TRACKS = 18712` |
| MIDI track size in a pattern | **2,233** | arithmetic: 36,588 − 8 − 8×2,338 − 12-byte tail = 8×2,233. octapy's `MIDI_TRACK_SIZE = 0x4AC` (1,196) does **not** match this; treat 2,233 as the pattern-track size |
| Parts start | `0x8EED6` (585,430) | octapy `BankOffset.PARTS`; first of eight `PART` headers, 6,331 bytes apart, in the factory bank |
| Part size | 6,331 (`0x18BB`) | octapy; 8 headers in the factory bank (4 working + 4 saved) |
| Trigger mask / trigless / plock mask | track+9 / +17 / +25 | octapy `AudioTrackOffset`; OSSC |
| P-locks | track+98, 64×32 | octapy, OSSC |
| Micro-timing / repeats / conditions | track+2210, 64×2 | octapy `TRIG_CONDITIONS = 2210` |

Rendered `Pattern` docs confirm the field *order* (header, 8 audio tracks, 8 MIDI tracks, scale, chain, unknown, part assignment, tempo_1, tempo_2) and the `PTRN….` header, but not the byte sizes — sizes come from octapy + empirical magic spacing.

Trig-mask **byte order** (cleanly sourced + empirically confirmed on Manager `real_device`): bit `(step−1) mod 8`, bytes reversed by half-page (byte 7 = steps 1–8). Confirmed: step 1 sets bit 0 of mask byte 7; step 16 sets bit 7 of byte 6. octapy `_trig.py`, OSSC `bankFile.js` / notes (OSSC also reports this as device-verified).

## 6. Checksum algorithm, range, endianness, blank value

**Algorithm (cleanly sourced, not from ot-tools source):** a wrapping unsigned 16-bit **sum of bytes**, stored as **u16 big-endian** in the last two bytes.

- **Banks:** OSSC `notes-formats.md:33` and `bankFile.js:114–115` specify the sum of `data[20 : len-2]`. Empirical: that sum matched the stored big-endian word on every remaining real 636,113-byte bank listed above, and also on OctaDoc's version-22 `bank13.strd`. Because header bytes 16–20 are zero, `sum(data[16:-2])` currently equals `sum(data[20:-2])`; implement the documented start (20) anyway.
- **markers.work / `.ot` / arrangements:** OSSC documents start **16** for markers and `.ot`; octapy `markers.py` uses `CHECKSUM_START = 0x14`. Empirical: Manager `real_device/markers.work` (207,000 bytes), factory `markers.work`, factory and Manager `arr*.work` / `blank_arrangement.work` (11,336 bytes) all match the wrapping u16 BE sum (start 16 and 20 are equal while bytes 16–19 are zero).
- Rendered `HasChecksumField` docs confirm a checksum exists and must be updated after edits; they do **not** document the byte range on the rendered page. We therefore do **not** rely on docs.rs for the algorithm, only for the fact of a checksum field.

**Wrong descriptions to avoid (allowed sources):** OctaDoc's `checksum.py` is a 16-bit XOR experiment and does not match these files. Manager's prose doc `docs/octatrack-file-writing.md` says the sum runs from byte 0; that contradicts both OSSC and the empirical stored values — ignore the prose, not the running Manager code (which calls a library checksum).

**Blank-bank checksum.** The previously published **0x96BB** value is **withdrawn** (it came only from deleted ot-tools blank fixtures). In the octapy **factory 1.40B** template, all 16 banks are identical and store **`0x999C`**, which matches the wrapping sum. Always *recompute* after a write; do not hard-code a golden constant.

Factory `markers.work` and all eight factory arrangements store checksum **`0x0004`** and **`0x0006`** respectively (header/version only, rest zero) — also a wrapping-sum match.

## 7. `markers.work`

**Cleanly sourced.** Always present in the factory template and in Manager `real_device`. Size **207,000** bytes. Header `FORM\0\0\0\0DPS1SAMP\0\0\0\0\0`, version **4**, then Flex / recorder / Static slot records, then u16 BE checksum (OSSC notes; rendered `MarkersFile` field order: header, version, `flex_slots`, `recording_buffers`, `static_slots`, checksum; octapy markers I/O).

Rendered docs.rs crate page, “Octatrack File Relationships”: loading a sample into a slot updates both the project file and the markers file; `.ot` (`SampleSettingsFile`) is written when saving sample attributes from the audio editor, and is read back when loading a sample into a slot. OSSC: the device applies trim/slice data from **`markers.work`**, not from `.ot` sidecars, so a builder that assigns slots in `project.work` must also write `markers.work` if slices/trim should appear.

A disabled slice loop point is `0xFFFFFFFF` (docs.rs “Default Loop Point Values”; OSSC; OctaChainer).

## 8. `.ot` sidecar

**Cleanly sourced** from OctaChainer (Unlicense) via OSSC `notes-formats.md` (and OctaChainer `otwriter.h/.cpp` in the remaining clone): 832 bytes, header `FORM\0\0\0\0DPS1SMPA`, multi-byte fields big-endian, gain u16 at 0x2B (48 = 0 dB), timestretch 0/2/3 (off/normal/beat), checksum = wrapping u16 sum of bytes 16..829. Optional; needed when we want the file-browser import path to carry attributes. takoyaki's `sample.ot` fixture is **not** a real `.ot` and was ignored. We do not currently have a remaining *real* `.ot` in the allowed corpus, so treat the 832-byte layout as sourced from OctaChainer/OSSC and **confirm with a hardware save** of sample attributes.

## 9. Arrangement files

**Cleanly sourced enough to write blanks; skip-on-boot still unverified.** Manual: a project contains 8 arrangements. Factory template and Manager `blank_arrangement.work` / `real_device/arr01.work` are **11,336** bytes, header `FORM\0\0\0\0DPS1ARRA\0\0\0\0\0`, version **6**, checksum matches the wrapping sum. **Whether a project boots with arrangements omitted is UNSOURCED on hardware** — no allowed source tests that. Write all eight blank arrangements (the factory files are a valid blank; we can also emit the same zero body + checksum `0x0006` without shipping the zip).

## 10. Micro-timing encoding (track+2210)

**Behaviour (cleanly sourced, official manual pp. 76–77, §12.12–12.13):** TRIG OFFSET places a trig on a **1/384-note** micro-timing grid (the same manual, in PERSONALIZE, also calls this **96 ppqn**). The trig can be nudged forwards or backwards relative to its step. TRIG COUNT 2–8 adds evenly spaced repeats. TRIG CONDITION is on the same menu (FILL, PRE, NEI, probabilities, A:B, etc.).

**File encoding (cleanly sourced from allowed repos + Manager tests, not ot-tools source):** octapy puts the 64×2-byte records at track **+2210**. Manager `project_reader.rs:1403–1439` and tests around `:20900` decode two bytes `[A, B]`:

- `u = ((A & 0x1F) << 1) | (B >> 7)` is a 6-bit two's-complement tick offset; `k = u−64 if u≥32 else u`.
- `A>>5` is `repeats−1` (0 = one hit, up to 7 = 8×).
- `B & 0x7F` is the condition code.

This closed form matches every Manager test vector we checked (`+23/384` = `(11,128)`, `−23/384` = `(20,128)`, `+1/384` = `(0,128)`, `−1/384` = `(31,128)`, `±1/32`, etc.) and is invertible on **−23…+23**. The official manual does not state the ±23 numeric limit; that range is from the Manager decoder (47 named settings). **Confirm the extreme values with a hardware toggle** (nudge to the last grid point, dump the two bytes). If a groove point cannot land on that grid, place the trig one tick early and add leading silence in the WAV.

In the factory bank the +2210 region is all zeros (on-grid, 1×, no condition).

## 11. P-lock `0xFF` sentinel

**Cleanly sourced.** octapy `PLOCK_DISABLED = 255`; OSSC notes “255 = no lock.” Empirical: every audio-track p-lock byte in the octapy factory bank is `0xFF` (262,144/262,144). Remaining Manager/OctaDoc banks are `0xFF` except on steps that clearly carry real parameter values (e.g. Manager `real_device/bank01.strd` pattern 1/7 track 3 has non-255 machine bytes; all other bytes on those records stay `0xFF`). Unlocked parameters must remain `255`.

## 12. Sample-lock bytes (+30 vs +31)

**Partly sourced; hardware byte for “both machine types” is not settled in the remaining corpus.**

- Manager (`project_reader.rs:1977–1986`) says the per-trig sample lock is stored in `flex_slot_id` (0-based) **for both Static and Flex machines**, 255 = unlocked; UI is 1-based.
- OSSC says write **byte +31** as 255 when not using a sample lock, and describes a 32-byte record ending in `flex_slot_id, static_slot_id`.
- octapy names `STATIC_SLOT_ID = 30` and `FLEX_SLOT_ID = 31` — a split that conflicts with Manager's “flex_slot_id for both.”

**No remaining allowed fixture contains a sample lock** (Manager even comments that its shipped banks hold none; our scan of `real_device`, `lfo_modes`, `multipart`, and OctaDoc banks found `+30`/`+31` unused). The earlier “hardware always uses +31” recount came from deleted ot-tools fixtures and is **withdrawn**.

**UNSOURCED, re-derive:** which of +30 / +31 the device writes for a Static-track sample lock and for a Flex-track sample lock. **Plan:** on a spare CF / separate set, save a blank project, add a sample lock on one Static track and one Flex track, copy the bank off, and diff against the blank. Tentative write rule until then: follow Manager + OSSC's +31 byte and leave the other at `0xFF`, and include that diff in the hardware checklist. Do not ship either field layout as gospel.

## 13. The +25 “plock / trigless” mask

**Ordinary sample trigs: cleanly sourced empirically. Exact meaning of +25: still to confirm.**

In Manager `real_device/bank01.strd`, steps that carry p-lock bytes have the **+25 mask entirely clear**. A sample trig (step 1) has the **+9 trigger** bit set; a trigless step (step 16) has the **+17 trigless** bit set. So we should **not** require +25 for ordinary (or even these trigless) parameter locks. That agrees with OSSC's hardware-verified write path (trigger mask + p-lock bytes only).

What +25 actually flags (possibly *lock trigs* as distinct from trigless, per the manual §12.4.3–12.4.4) is **UNSOURCED, re-derive**: on hardware, convert a step to a lock trig vs a trigless trig and diff the three 8-byte masks.

## 14. Recommended strategy — does it change?

**The overall strategy does not change:** clean-room MIT writer, no GPL/AGPL dependencies, no bundled factory binaries (including octapy's zip), 44.1 kHz 16/24-bit WAV copies in `AUDIO`, write all 26 `.work` files into a **new** project folder that is not currently loaded, encode micro-timing on the 1/384 grid, pad leading silence when needed, recompute wrapping u16 BE checksums, test only on a spare CF card or a separate set (never anyone else's projects).

**What does change** is the evidence base and a few details:

- Do not re-clone or re-read ot-tools source (or docs.rs `/src/`). Use this file, rendered docs.rs, allowed repos, the official manual, and hardware captures.
- Do not hard-code checksum `0x96BB`; recompute. A factory blank bank in the octapy template stores `0x999C`.
- Do not require 136 `[SAMPLE]` blocks; follow the factory shape plus the slots we assign.
- Sample-lock +30/+31 and the precise role of mask +25 need a hardware toggle-and-diff before we freeze them.
- Withdraw any statistic that depended on ot-tools `test-data`.

**Hardware test plan (unchanged location, tighter diffs):** spare CF or a separate set only. Host pre-flight: sizes, headers, version 23, checksums, 44.1 kHz, forward-slash paths. On device: CHANGE PROJECT to the new folder, no parse/checksum/sample-load errors, play patterns, confirm slot assignment, audition micro-timing vs the grid, then — for re-derivation — toggle one TRIG OFFSET to an extreme, one sample lock on Static and one on Flex, one lock trig vs trigless, SAVE, copy the bank off, and diff. Then SAVE PROJECT, confirm `.strd` appear, RELOAD, power-cycle.

## Provenance table

| Point | Status | Sources |
| --- | --- | --- |
| Set / `AUDIO` / 16 banks / 8 arrangements / 44.1 kHz WAV-AIFF | Clean | Official manual pp. 27–28 |
| 26 `.work` files for a new project | Clean | octapy factory template; docs.rs `.work`/`.strd` rules |
| `.work` vs `.strd` semantics | Clean | docs.rs crate page; factory vs `real_device`; Manager open path |
| `project.work` sections and keys | Clean | factory template; Manager `real_device`; OSSC notes |
| Minimum `project.work` = 136 slots | Withdrawn / not required | Factory file lists only 8 recorders; hardware-test our writer |
| Bank size 636,113, header, version 23 | Clean | octapy; docs.rs `BankFile`; OSSC; remaining banks |
| Pattern 36,588 / audio 2,338 / parts 6,331 and offsets | Clean | octapy + empirical `PTRN`/`TRAC`/`PART` spacing |
| Checksum wrapping u16 BE, banks start 20 | Clean | OSSC `bankFile.js` / notes; matched on remaining real files |
| Blank checksum `0x96BB` | Withdrawn | Was ot-tools-only. Factory banks store `0x999C` |
| `markers.work` 207,000, header, version 4 | Clean | factory + Manager `real_device`; OSSC; docs.rs `MarkersFile` |
| `.ot` 832-byte layout | Clean from OctaChainer/OSSC | Confirm with a hardware-saved `.ot` (none left in corpus) |
| Arrangements 11,336, header, version 6 | Clean | factory; Manager template/`real_device` |
| Skip arrangements and still boot | UNSOURCED, re-derive | Write all eight; hardware: try omitting them only on a spare set |
| Micro-timing 1/384, TRIG COUNT, conditions (behaviour) | Clean | Official manual pp. 76–77 |
| Micro-timing bytes at +2210, ±23 encode/decode | Clean from Manager + octapy | Confirm extremes with a hardware dump |
| P-lock sentinel `0xFF` | Clean | octapy, OSSC, factory + remaining banks |
| Sample lock always at +31 for both machines | UNSOURCED, re-derive | Allowed sources disagree; no remaining fixture has a sample lock |
| +25 mask required for p-locks | Not required for ordinary trigs | `real_device` p-locked steps have +25 clear; confirm lock-trig meaning on hardware |
| Backslash PATH “catastrophic” device rewrite | UNSOURCED, re-derive | Quote removed; still write `/` only |

## Notes on superseded survey drafts

Superseded format-survey drafts are not in this repository. Verbatim ot-tools-io source-comment quotations and docs.rs `/src/` pointers were removed from those drafts before this file was written. File:line citations into ot-tools source are withdrawn as a path to that source.
