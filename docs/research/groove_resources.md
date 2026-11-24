# Ghostbreak Rhythm & Groove Research Survey

**Target system:** Ghostbreak — a planned tool that generates fresh drum breaks from a user's own one-shot samples, exports Elektron Octatrack projects, learns groove parameters (microtiming, swing, velocity/accent, pitch shifts) from existing breaks, and adapts future generations from user ratings.  
**Target genres:** Drum and bass (DnB), jungle, breakbeat, house, and groove-forward techno sub-genres (minimal, dub techno, rominimal, UK techno / UK bass-adjacent grooves).  
**Report scope & methodology:** Survey of real, citable datasets, academic literature, software tools/libraries, and genre-specific rhythmic reference sources. Every claim is grounded in primary sources (official repositories, Zenodo records, DOIs, arXiv preprints, publisher pages, and statutory texts). Unverified details are explicitly marked **`UNVERIFIED`** with reasons. Licensing notes are engineering compliance flags, **not legal advice**.

---

## Quick Licensing Compatibility Legend (for a Future MIT / Commercial Public Tool)

| Licence / Terms | Compatible with MIT Core? | Commercial Use Allowed? | Key Engineering Implication |
| :--- | :--- | :--- | :--- |
| **MIT / ISC / BSD-2 / BSD-3 / Apache-2.0** | Yes | Yes | Safe direct code dependency (retain notices; Apache-2.0 adds patent/NOTICE clauses). |
| **CC0-1.0 / Public Domain** | Yes | Yes | Safe for bundled templates, metadata, and training data. |
| **CC BY 4.0** | Yes (for data/assets with attribution) | Yes | Safe for training open/commercial models and distributing derivatives with attribution. |
| **CC BY-NC / CC BY-NC-SA / CC BY-NC-ND** | **No** (for shipped weights/assets in commercial use) | **No** | Research/benchmarking only; do **not** ship weights or train commercial release models without separate permission. |
| **GPL-3.0 / AGPL-3.0** | **No** (strong copyleft) | Only under GPL/AGPL terms or commercial dual-licence | Avoid as an embedded library in an MIT core; reference-only unless Ghostbreak adopts GPL/AGPL or isolates/re-implements from specs. |
| **No LICENSE file on GitHub** | **No** | **No** (All Rights Reserved by default) | Reference-only; cannot copy or embed code/weights without explicit permission. |

---

## (a) Datasets with Drum Onsets, MIDI, and/or Microtiming

### 1. Groove MIDI Dataset (GMD) — Google Magenta
* **URL:** <https://magenta.tensorflow.org/datasets/groove> (accompanying paper: <https://arxiv.org/abs/1905.06118>)
* **What it gives us:** Human-performed expressive drumming captured on a Roland TD-11 electronic drum kit (10 drummers, mostly professionals). Provides time-aligned MIDI (exact unquantized onset microtiming, note velocities, tempo/time-signature metadata, genre labels, beat vs. fill tags) plus synthesized audio. Genre coverage leans heavily toward funk, rock, jazz, soul, hip-hop, Latin, and Afrobeat—light on electronic dance sub-genres, though funk/soul/hip-hop grooves share structural ancestry with breakbeats.
* **Licence / Commercial & MIT compatibility:** **CC BY 4.0** (data). Compatible with a future MIT-licensed and/or commercial Ghostbreak tool and publicly released models, provided attribution is maintained.
* **Size:** ~13.6 hours of audio/MIDI; 1,150 MIDI files; >22,000 measures.
* **Quality:** Very high timing and velocity fidelity for human microtiming research; widely adopted gold standard for symbolic groove modeling. Main limitation is single-kit synthesized timbre and sparse electronic/jungle/techno genre representation.
* **Recommendation:** **Adopt first.** Primary clean, commercially safe training and validation dataset for Ghostbreak's symbolic groove representation (microtiming offsets, swing profiles, velocity/accent contours).

### 2. Expanded Groove MIDI Dataset (E-GMD) — Google Magenta
* **URL:** <https://magenta.tensorflow.org/datasets/e-gmd> (accompanying paper: <https://arxiv.org/abs/2004.00188>)
* **What it gives us:** Re-recordings of GMD's human MIDI performances through 43 distinct drum kits (both acoustic kits and classic electronic kits such as TR-808 and TR-909) on a Roland TD-17 module. Provides aligned audio, drum onsets, exact microtiming, and—crucially—**explicit per-hit velocity annotations** aligned to audio.
* **Licence / Commercial & MIT compatibility:** **CC BY 4.0**. Fully compatible with MIT/commercial Ghostbreak development and model training with attribution.
* **Size:** 444.5 hours of audio across 45,537 sequences (43 kits).
* **Quality:** High temporal alignment accuracy and unique large-scale velocity labels; includes electronic drum kit timbres relevant to house/techno/breakbeat. Still lacks full-mix background bleed and vintage vinyl-compressed acoustic breaks.
* **Recommendation:** **Adopt first (for ADT + velocity estimation).** Ideal commercially safe dataset if Ghostbreak trains or fine-tunes its own lightweight drum onset + velocity estimator to extract grooves from user-supplied drum loops.

### 3. StemGMD (Politecnico di Milano — ISPL)
* **URL:** <https://zenodo.org/records/7860223> (Part 1) and <https://zenodo.org/records/7882857> (Part 2); companion repo <https://github.com/polimi-ispl/larsnet>
* **What it gives us:** Isolated single-instrument acoustic drum stems rendered from GMD MIDI across multiple drum kits, aligned with the original GMD MIDI microtiming and velocity annotations. Built specifically for drum source separation (kick, snare, toms, hi-hat, cymbals) and transcription.
* **Licence / Commercial & MIT compatibility:** **CC BY 4.0** for the StemGMD dataset (compatible with MIT/commercial training with attribution). *Important split:* the authors' pretrained **LarsNet** model weights are **CC BY-NC 4.0** (non-commercial).
* **Size:** 1,224 hours of audio across 103,500 clips (~1.13 TB uncompressed; smaller subsets ~43 GB).
* **Quality:** High clean-stem isolation and exact ground-truth MIDI alignment inherited from GMD; acoustic-kit bias rather than heavily processed DnB/techno breaks.
* **Recommendation:** **Defer to Phase 2.** Too large (~1.13 TB) for an initial prototype, but a strong commercially safe resource if Ghostbreak later trains its own commercial drum-stem separator for cleaner microtiming extraction from dense breaks.

### 4. ENST-Drums (Télécom Paris)
* **URL:** Official Télécom Paris project page: <https://perso.telecom-paristech.fr/grichard/ENST-drums/> *(Note: a commonly cited Zenodo mirror URL could not be verified — record `7443831` returned HTTP 404; use the official Télécom Paris page for access/terms).*
* **What it gives us:** Multi-channel audio and video recordings of 3 professional drummers playing real acoustic kits across styles (rock, pop, funk, jazz, Latin), with wet mixes, dry close-mic tracks, minus-one accompaniment, and manually verified onset annotations across ~20 drum components. No native MIDI velocity ground truth.
* **Licence / Commercial & MIT compatibility:** **CC BY-NC-ND 4.0** (non-commercial, no derivatives; research use only). **Incompatible** with commercial model training or redistribution in an MIT public product.
* **Size:** 8.6 GB total (~4.2 GB audio archive, ~4.4 GB video archive); ~75 minutes per drummer (~3.75 hours total).
* **Quality:** Classic, carefully curated acoustic ADT benchmark with real microphone bleed and natural dynamics, though small by modern ML standards and older in genre mix.
* **Recommendation:** **Internal benchmark / evaluation only.** Useful for validating Ghostbreak's transcription accuracy on real acoustic drums during R&D; never train shipped weights on it.

### 5. IDMT-SMT-Drums (Fraunhofer IDMT)
* **URL:** <https://zenodo.org/records/7544164> (overview: <https://www.idmt.fraunhofer.de/en/publications/datasets.html>)
* **What it gives us:** 44.1 kHz / 16-bit mono WAV audio of polyphonic drum loops and isolated single-instrument tracks (acoustic kits, drum machines, and synthesized samples) paired with onset annotations for kick, snare, and hi-hat. Includes electronic/synthesized drum kits alongside acoustic kits.
* **Licence / Commercial & MIT compatibility:** **CC BY-NC-ND 4.0** (evaluation / non-commercial only). **Incompatible** with commercial training or MIT redistribution.
* **Size:** 608 WAV files (~2 hours 10 minutes total, including 104 polyphonic drum loops).
* **Quality:** Clean, widely cited evaluation standard for 3-class ADT (kick/snare/hi-hat) with helpful electronic timbre coverage, but small and lacks rich microtiming/velocity/multi-piece annotations.
* **Recommendation:** **Internal benchmark only.** Good sanity-check test suite for Ghostbreak's break-analysis front end, especially for electronic/drum-machine loops.

### 6. MDB Drums (Carl Southall et al., ISMIR 2017)
* **URL:** <https://github.com/CarlSouthall/MDBDrums>
* **What it gives us:** Real-world multi-track songs derived from MedleyDB (MusicDelta subset) with drum-only mixes, full mixes, individual WAV stems, and manually annotated drum onsets across 6 primary classes (kick, snare, hi-hat, toms, cymbals, other percussion) and 21 technique subclasses.
* **Licence / Commercial & MIT compatibility:** **CC BY-NC-SA 4.0** (audio and annotations). **Incompatible** with commercial use or MIT-licensed redistribution.
* **Size:** 23 tracks (~54 seconds average; ~20–21 minutes of audio per mix type); 7,994 annotated drum onsets.
* **Quality:** High annotation precision on real studio recordings with full-mix context, but very small and mostly rock/pop/funk/jazz rather than club genres.
* **Recommendation:** **Internal benchmark only.** Useful to test whether Ghostbreak's onset extractor degrades when a user feeds a non-isolated mix instead of a clean drum break.

### 7. ADTOF Dataset (Mickaël Zehren, Marco Alunno, & Paolo Bientinesi, ISMIR 2021)
* **URL:** Zenodo dataset: <https://doi.org/10.5281/zenodo.5624527>; code repo: <https://github.com/MZehren/ADTOF>; paper: <https://arxiv.org/abs/2111.11737>
* **What it gives us:** Large-scale non-synthetic, real-world music annotated for 5-class automatic drum transcription (kick, snare, toms, hi-hat, cymbals) derived from rhythm-game drum charts aligned to real recordings. Distributed as mel-spectrogram features rather than raw audio (due to underlying recording copyrights).
* **Licence / Commercial & MIT compatibility:** Code is **CC BY-NC-SA 4.0**. Dataset (via Zenodo request) is restricted to **internal non-commercial academic/research use** (no redistribution, modification, or audio reconstruction). **Incompatible** with MIT/commercial Ghostbreak releases.
* **Size:** 359 hours of real-world music (mel-spectrograms + aligned drum onset labels).
* **Quality:** One of the strongest real-world ADT training corpora in existence (dramatically outperforms synthetic-only models on full mixes), though chart quantization and rock/metal skew limit microtiming fidelity and club-music coverage.
* **Recommendation:** **Reference / research benchmark only.** Do not bundle ADTOF weights or train Ghostbreak's public release on ADTOF due to strict NC/internal-only terms. Study its data-cleaning and architecture recipes instead.

### 8. Slakh2100 (Manilow et al., WASPAA 2019)
* **URL:** <https://zenodo.org/records/4599666>
* **What it gives us:** Synthesized multi-instrument mixes with isolated stems and time-aligned MIDI (every track includes a Drums stem + MIDI on GM drum channel). Rendered using professional sample libraries (e.g., Native Instruments Kontakt patches) from Lakh MIDI Dataset v0.1 files.
* **Licence / Commercial & MIT compatibility:** Listed on Zenodo under **CC BY 4.0**, **HOWEVER** provenance carries meaningful risk: underlying MIDI files originate from web-scraped Lakh MIDI v0.1 (many derived from copyrighted commercial songs) and audio was rendered through commercial virtual instruments. Treat commercial/MIT release compatibility as **CAUTION / UNVERIFIED for commercial model distribution** without independent legal review.
* **Size:** 2,100 tracks (~145 hours); ~105 GB compressed FLAC (~500 GB uncompressed WAV).
* **Quality:** Clean multi-track alignment and diverse acoustic/rock/pop drum arrangements, but MIDI timing is largely quantized/sequenced rather than live expressive microtiming, and EDM/DnB representation is limited.
* **Recommendation:** **Defer / low priority.** GMD and E-GMD are vastly better for microtiming, velocity, and clean provenance. Only consider Slakh2100 for academic source-separation ablations.

### 9. Freesound Loop Dataset (FSL10K — Ramires et al., ISMIR 2020)
* **URL:** <https://zenodo.org/records/3967852>; paper: <https://arxiv.org/abs/2008.11507> (*"The Freesound Loop Dataset and Annotation Tool"*, António Ramires, Frederic Font, Dmitry Bogdanov, Jordan B. L. Smith, & Sergi Serra, ISMIR 2020)
* **What it gives us:** Real electronic and acoustic audio loops sourced from Freesound.org, including drum/percussion loops spanning electronic dance genres (house, techno, drum & bass, breakbeat, hip-hop), with BPM, key, genre, instrumentation tags, and Essentia/AudioCommons analyses. Does **not** include ground-truth MIDI or per-hit drum onset annotations out of the box.
* **Licence / Commercial & MIT compatibility:** **Per-file mixed Creative Commons** (CC0, CC BY, CC BY-NC, etc.), recorded in `FSL10K/metadata.json`. **Compatible with MIT/commercial use ONLY if filtered strictly to CC0 and CC BY tracks** (excluding NC and SA/ND as needed).
* **Size:** 9,455 audio loops (~3,000 with expert/crowd web annotations).
* **Quality:** Rare openly licensed corpus containing actual user-created electronic/dance/breakbeat audio loops rather than rock/jazz kit recordings; quality varies because Freesound is crowdsourced.
* **Recommendation:** **Adopt in Phase 1/2 (CC0 + CC BY subset only).** Best legally clean pool of real electronic/breakbeat drum loops for testing Ghostbreak's break-ingestion, beat-tracking, and groove-extraction pipeline.

### 10. GiantSteps Drum Pattern Datasets (Vogl & Knees)
* **URL:** <https://github.com/GiantSteps/drum-pattern-datasets>
* **What it gives us:** Lightweight symbolic polyphonic drum pattern collections targeting electronic dance music (EDM), compiled for EDM rhythm analysis and generation research. Primarily grid-based symbolic drum patterns (JSON/CSV/MIDI-style representations) rather than raw microtimed audio performances.
* **Licence / Commercial & MIT compatibility:** **No top-level `LICENSE` file detected on GitHub** (verified via GitHub API; defaults to All Rights Reserved unless a per-subdirectory licence or author permission clarifies open/CC0 terms—exact redistribution terms remain **`UNVERIFIED`**).
* **Size:** Lightweight symbolic repository (a few MB).
* **Quality:** Directly relevant to EDM/house/techno drum-pattern structure (step densities, syncopation, genre archetypes), though limited in microtiming nuance because patterns are largely quantized.
* **Recommendation:** **Reference only unless licensing is clarified.** Do not bundle directly into an MIT release without verifying or obtaining explicit permissive licensing from the authors.

### 11. Jungle / Amen-Break / Breakbeat Annotation Resources (Gap Assessment)
* **URLs:**
  * MIR dataset index: <https://github.com/ismir/mir-datasets>
  * Frequency-band loop onset/bassline dataset: <https://github.com/behzadhaki/drum_bassline_dataset>
  * Community Amen slice/pattern scripts: <https://github.com/schollz/amenbreak>
  * MIDI drum archive indexer: <https://github.com/caedmon5/midi_drum_indexer>
* **What it gives us:** **Critical finding:** There is currently **no** large, peer-reviewed, openly licensed MIR dataset of ground-truth microtimed drum onsets/MIDI specifically covering classic jungle, DnB, or breakbeat records—primarily because canonical breaks are copyrighted sound recordings (see Section (d)). Existing artifacts are fragmented: (i) academic microtiming measurements published only as tables/figures in papers (notably Frane 2017 for classic hip-hop/breakbeat canon breaks), (ii) `behzadhaki/drum_bassline_dataset` (quantized 8-band frequency onset representations of drum loops), and (iii) community live-coding/hardware slice scripts like `schollz/amenbreak`.
* **Licence / Commercial & MIT compatibility:** Varies per repo: `schollz/amenbreak` code is **MIT License** (verified via GitHub API; never bundle copyrighted Amen audio clips), whereas `behzadhaki/drum_bassline_dataset` has **no GitHub `LICENSE` file** (= All Rights Reserved by default). Audio-provenance clearance for community Amen scripts is **`UNVERIFIED`**—do **not** bundle any Amen-derived audio clips.
* **Size:** Small / fragmented (hundreds of scripts or narrow band-onset files rather than multi-hour annotated breakbeat corpora).
* **Quality:** Insufficient as a standalone supervised training corpus; valuable only as conceptual references for slice-based break modeling and 16th-note/sub-beat grid conventions.
* **Recommendation:** **Do not rely on external breakbeat datasets.** Instead, design Ghostbreak to (1) learn general human microtiming/accent priors from CC BY 4.0 GMD/E-GMD, (2) extract groove parameters *locally at runtime* from breaks the user already owns, and (3) optionally build a small internal CC0 benchmark of newly recorded/programmed breakbeat loops for testing.

### 12. Other Secondary Datasets Noted in Literature (TMIDT, RBMA 13, A2MD)
* **URLs:** TMIDT paper: <https://doi.org/10.48550/arXiv.1806.06676> (direct Zenodo download URL `UNVERIFIED`); A2MD paper: <https://doi.org/10.1109/ICASSP39728.2021.9414409> and companion repo <https://github.com/Sma1033/adt_with_a2md> (also evaluates RBMA 13).
* **What it gives us:**
  * **TMIDT** (Vogl et al., DAFx-18): Large synthetic multi-instrument drum transcription dataset covering a full kit beyond 3-class kick/snare/hi-hat.
  * **RBMA 13** (Red Bull Music Academy 13): Small 13-track real-world multi-instrument ADT evaluation set.
  * **A2MD** (Wei et al., ICASSP 2021): >1,500 internet-sourced tracks with semi-automatic audio-to-MIDI drum alignment.
* **Licence / Commercial & MIT compatibility:** **Mixed / Non-commercial / Unclear** (especially A2MD's internet-sourced recordings and RBMA 13 audio; exact TMIDT download licence `UNVERIFIED`). Not safe for commercial model release.
* **Size:** RBMA 13 = 13 tracks; A2MD = >1,500 tracks; TMIDT exact GB/hour count `UNVERIFIED`.
* **Quality:** Standard academic ADT benchmarks; superseded by E-GMD/StemGMD for commercially clean data and ADTOF for academic real-mix scale.
* **Recommendation:** **Defer / reference only.**

---

## (b) Papers on Microtiming, Swing, Groove, Breakbeats, ADT, and Preference Learning

*(Note: For academic publications, "Licence" indicates open-access vs. paywalled publisher terms—ideas and mathematical methods are not copyrighted, though text/figures and companion code carry their own licences. "Size" indicates publication format/length.)*

### 1. GrooVAE — Gillick et al. (ICML 2019)
* **Full Citation:** Jon Gillick, Adam Roberts, Jesse Engel, Douglas Eck, & David Bamman (2019). *Learning to Groove with Inverse Sequence Transformations*. Proceedings of the 36th International Conference on Machine Learning (ICML), PMLR 97:2269–2279.
* **URL / DOI:** <https://arxiv.org/abs/1905.06118> | DOI: <https://doi.org/10.48550/arXiv.1905.06118>
* **What it gives us:** Introduces the Groove MIDI Dataset and **GrooVAE**, using seq2seq VAEs to perform **humanization** (quantized pattern -> expressive microtiming + velocity) and **Tap2Drum** (sparse rhythm prompt -> full multi-voice drum groove). Formulates the clean **HVO (Hits, Velocities, Offsets)** 16th-note tensor representation: per step and per drum voice, a binary hit, velocity $v \in [0, 1]$, and timing offset $o \in [-0.5, 0.5]$ steps.
* **Licence / Commercial & MIT compatibility:** Paper open access on arXiv/PMLR; GMD data **CC BY 4.0**; Magenta reference code **Apache-2.0**. Fully compatible with MIT/commercial Ghostbreak.
* **Size:** 11-page conference paper + 13.6-hour dataset.
* **Quality:** Seminal, battle-tested formulation. The HVO representation maps almost 1-to-1 onto an Elektron Octatrack step sequencer (trig, velocity/vol p-lock, microtiming trig offset!).
* **Recommendation:** **Adopt the HVO representation immediately** as Ghostbreak's core symbolic groove schema (extended with pitch/retrig/sample-slot parameters for breakbeats and Octatrack p-locks).

### 2. Dittmar, Pfleiderer et al. on Swing Ratios & the Swingogram (ISMIR 2015; JNMR 2018)
* **Full Citations:**
  1. Christian Dittmar, Martin Pfleiderer, & Meinard Müller (2015). *Automated Estimation of Ride Cymbal Swing Ratios in Jazz Recordings*. Proceedings of ISMIR 2015, pp. 271–277.
  2. Christian Dittmar, Martin Pfleiderer, Stefan Balke, & Meinard Müller (2018). *A Swingogram Representation for Tracking Micro-Rhythmic Variation in Jazz Performances*. Journal of New Music Research, 47(2), 97–113.
* **URL / DOI:** JNMR DOI: <https://doi.org/10.1080/09298215.2017.1367405> (ISMIR 2015 open-access proceedings; exact ISMIR PDF URL `UNVERIFIED`).
* **What it gives us:** Signal-processing methods for estimating swing ratios directly from audio recordings without clean MIDI, plus the **Swingogram**—a time-varying visualization and feature representation that tracks how swing ratios evolve bar-by-bar rather than collapsing a track to a single static swing number.
* **Licence / Commercial & MIT compatibility:** ISMIR 2015 open access; JNMR 2018 publisher copyright (methods freely implementable in MIT code).
* **Size:** 7-page conference paper (2015); 17-page journal article (2018).
* **Quality:** Rigorous MIR methodology grounded in jazz corpus analysis; transfers well to 16th-note swing estimation in house, UK techno, rominimal, and funk breakbeats.
* **Recommendation:** **Adopt the Swingogram concept** for Ghostbreak's groove analyzer—measuring *local* per-step/per-bar swing and microtiming drift rather than a single global scalar, especially useful for rominimal/dub-techno hypnotic variation and organic funk breaks.

### 3. Friberg & Sundström (2002) — Foundational Swing Ratio Curve
* **Full Citation:** Anders Friberg & Andreas Sundström (2002). *Swing Ratios and Ensemble Timing in Jazz Performance: Evidence for a Common Rhythmic Pattern*. Music Perception, 19(3), 333–349.
* **URL / DOI:** <https://doi.org/10.1525/mp.2002.19.3.333>
* **What it gives us:** Demonstrates empirically that human swing ratios are **tempo-dependent** (higher swing ratios at slower tempos, compressing toward 1:1 / straight at faster tempos—crucial when scaling breaks from 100 BPM funk up to 170–175 BPM DnB/jungle!) and that ensemble instruments have systematic asynchronous offsets (e.g., soloist vs. ride cymbal).
* **Licence / Commercial & MIT compatibility:** Paywalled journal article (methods/empirical findings freely usable).
* **Size:** 17-page journal article.
* **Quality:** Cornerstone empirical paper cited across almost all swing literature.
* **Recommendation:** **Adopt tempo-dependent swing scaling** in Ghostbreak so grooves extracted at one BPM (e.g., 124 BPM house or 105 BPM funk break) scale musically when rendered at 172 BPM jungle/DnB.

### 4. Andrew V. Frane (2017) — Swing Rhythm in Classic Drum Breaks
* **Full Citation:** Andrew V. Frane (2017). *Swing Rhythm in Classic Drum Breaks From Hip-Hop's Breakbeat Canon*. Music Perception, 34(3), 291–302.
* **URL / DOI:** <https://doi.org/10.1525/mp.2017.34.3.291>
* **What it gives us:** **Directly measures 16th-note swing ratios and microtiming in canonical sampled drum breaks** (the funk/soul breakbeat canon shared by hip-hop, jungle, and breakbeat). Shows how non-uniform, uneven 16th-note swing and ghost-note placements—rather than simple straight grids or uniform triplet swing—define the feel of classic breaks.
* **Licence / Commercial & MIT compatibility:** Paywalled journal article (empirical findings and analytical formulas freely usable; note underlying breaks remain copyrighted recordings).
* **Size:** 12-page journal article.
* **Quality:** Directly on target for Ghostbreak's breakbeat domain—one of the rare peer-reviewed quantitative timing studies of classic breaks themselves.
* **Recommendation:** **High-priority reference.** Use Frane's measurement methodology and empirical swing-ratio ranges to validate Ghostbreak's groove extractor and parameter bounds.

### 5. Matthew W. Butterfield (2010, 2011) — Microtiming, Participatory Discrepancies, & Anacrusis
* **Full Citations:**
  1. Matthew Butterfield (2010). *Participatory Discrepancies and the Perception of Beats in Jazz*. Music Perception, 27(3), 157–176.
  2. Matthew W. Butterfield (2011). *Why Do Jazz Musicians Swing Their Eighth Notes?* Music Theory Spectrum, 33(1), 3–26.
* **URL / DOI:** 2010: <https://doi.org/10.1525/mp.2010.27.3.157> | 2011: <https://doi.org/10.1525/mts.2011.33.1.3>
* **What it gives us:** Shows that listeners often cannot explicitly identify sub-30 ms microtiming offsets consciously, yet **asynchrony between bass/kick and snare/hi-hat** (e.g., snare slightly behind or ahead of kick/bass) shapes perceived propulsion ("anacrusis" / forward drive vs. laid-back pocket). Explains swing not just as timing delay, but as the interaction of **timing ratio + dynamic accentuation** on offbeats.
* **Licence / Commercial & MIT compatibility:** Paywalled journal articles (concepts freely usable).
* **Size:** 20-page and 24-page journal articles.
* **Quality:** Rigorous music-theoretical and perceptual synthesis.
* **Recommendation:** **Core design principle.** Ensure Ghostbreak models *inter-voice* microtiming offsets (e.g., snare vs. kick pocket, ghost-snare pull, hi-hat push) coupled with velocity/accent rather than applying one uniform timing shift to all drums.

### 6. Anne Danielsen — *Musical Rhythm in the Age of Digital Reproduction* (2010) & Related Beat-Bin / P-Center / EDM Papers
* **Full Citations:**
  1. Anne Danielsen (Ed.) (2010). *Musical Rhythm in the Age of Digital Reproduction*. Ashgate / Routledge. ISBN 978-1-138-24684-3 (hc) / 978-1-315-59698-3 (eBook).
  2. Anne Danielsen, Kristian Nymoen, Evan Anderson, Guilherme Schmidt Câmara, Martin Torvik Langerød, Marc R. Thompson, & Justin London (2019). *Where Is the Beat in That Note? Effects of Attack, Duration, and Frequency on the Perceived Timing of Musical and Quasi-Musical Sounds*. Journal of Experimental Psychology: Human Perception and Performance, 45(3), 402–418.
  3. Ragnhild Brøvig-Hanssen, Bjørnar E. Sandvik, & Jon Marius Aareskjold-Bakke (2020). *Dynamic Range Processing and Its Influence on Perceived Timing in Electronic Dance Music*. Music Theory Online, 26(2).
  4. Ragnhild Brøvig-Hanssen, Bjørnar Sandvik, Jon Marius Aareskjold-Drecker, & Anne Danielsen (2022). *A Grid in Flux: Sound and Timing in Electronic Dance Music*. Music Theory Spectrum, 44(1), 1–16.
* **URLs / DOIs:**
  * Book (2010): <https://doi.org/10.4324/9781315596983>
  * P-center paper (2019): <https://doi.org/10.1037/xhp0000611>
  * MTO paper (2020): <https://www.mtosmt.org/issues/mto.20.26.2/mto.20.26.2.brovighanssen.html>
  * MTS paper (2022): <https://doi.org/10.1093/mts/mtab013>
* **What it gives us:**
  * **Beat-bin model (2010):** A beat is not a zero-width grid line but a temporal window ("bin") whose width and shape govern rhythmic tolerance and groove feel—explaining how quantized and unquantized layers coexist in digital/club music.
  * **P-center / sample acoustics (2019):** **Crucial for Ghostbreak**—because Ghostbreak generates breaks from *arbitrary user one-shot samples*, a sample's **attack rise time, duration, and frequency content** shift its perceptual center (P-center) later than its file start / physical onset! Triggering a soft-attack shaker or subby kick on the exact same Octatrack microtiming tick as a sharp rimshot produces *different* perceived timing.
  * **EDM grid-in-flux & compression (2020, 2022):** Explains how house/techno/EDM producers create micro-rhythmic push/pull even on quantized grids via sample envelopes, sidechain compression, filtering, and timbre—directly explaining groove in minimal, dub techno, and rominimal.
* **Licence / Commercial & MIT compatibility:** Book/JEP/MTS paywalled; MTO is open access. All theoretical/DSP ideas freely implementable.
* **Size:** Edited monograph (2010) + three peer-reviewed journal articles.
* **Quality:** Gold standard for contemporary digital/electronic microrhythm research (RITMO / University of Oslo).
* **Recommendation:** **Adopt P-center compensation as a core Ghostbreak differentiator.** Estimate each user one-shot's perceptual onset / attack centroid and compensate Octatrack trig microtiming (or sample start trim) so learned grooves translate accurately across wildly different user sample kits.

### 7. Empirical Groove & Syncopation Perception — Davies et al. (2013), Witek et al. (2014), Kilchenmann & Senn (2015), Senn et al. (2018, 2024)
* **Full Citations:**
  1. Matthew Davies, Guy Madison, Pedro Silva, & Fabien Gouyon (2013). *The Effect of Microtiming Deviations on the Perception of Groove in Short Rhythms*. Music Perception, 30(5), 497–510. DOI: <https://doi.org/10.1525/mp.2013.30.5.497>
  2. Maria A. G. Witek, Eric F. Clarke, Mikkel Wallentin, Morten L. Kringelbach, & Peter Vuust (2014). *Syncopation, Body-Movement and Pleasure in Groove Music*. PLOS ONE, 9(4), e94446. DOI: <https://doi.org/10.1371/journal.pone.0094446>
  3. Lorenz Kilchenmann & Olivier Senn (2015). *Microtiming in Swing and Funk affects the body movement behavior of music expert listeners*. Frontiers in Psychology, 6, 1232. DOI: <https://doi.org/10.3389/fpsyg.2015.01232>
  4. Olivier Senn, Lorenz Kilchenmann, Toni Bechtold, & Florian Hoesl (2018). *Groove in drum patterns as a function of both rhythmic properties and listeners' attitudes*. PLOS ONE, 13(6), e0199604. DOI: <https://doi.org/10.1371/journal.pone.0199604>
  5. Olivier Senn, Florian Hoesl, Toni A. Bechtold, Lorenz Kilchenmann, Rafael Jerjen, & Maria Witek (2024). *Null effect of perceived drum pattern complexity on the experience of groove*. PLOS ONE, 19(11), e0311877. DOI: <https://doi.org/10.1371/journal.pone.0311877>
* **What it gives us:** Key empirical caveats: (i) excessive random microtiming *hurts* groove—structured, systematic microtiming (swing, consistent voice pocket) works far better than Gaussian jitter (Davies et al. 2013; Kilchenmann & Senn 2015); (ii) syncopation exhibits an inverted-U relationship with pleasure/urge to move (Witek et al. 2014), heavily modulated by individual listener taste (Senn et al. 2018, 2024).
* **Licence / Commercial & MIT compatibility:** PLOS ONE & Frontiers are Open Access (CC BY); Music Perception is paywalled.
* **Size:** Five peer-reviewed empirical journal articles.
* **Quality:** High statistical rigor; directly validates *why* Ghostbreak needs a **personalized user-rating preference loop** rather than a single universal "groove score."
* **Recommendation:** **Use as design guardrails:** keep microtiming structured (voice-level pocket + swing + subtle correlated drift, never blind random jitter) and personalize syncopation/density via user ratings.

### 8. Amen-Break, Jungle, & Electronic Dance Music Rhythm Literature
* **Full Citations:**
  1. Benjamin Noys (1995). *Into the Jungle*. Popular Music, 14(3), 321–332. DOI: <https://doi.org/10.1017/S0261143000007765>
  2. Mark J. Butler (2006). *Unlocking the Groove: Rhythm, Meter, and Musical Design in Electronic Dance Music*. Indiana University Press. ISBN 978-0-253-21804-9 (pb).
  3. Hans T. Zeiner-Henriksen (2010). *The "PoumTchak" Pattern: Correspondences between Rhythm, Sound, and Movement in Electronic Dance Music*. PhD dissertation, University of Oslo. (URL via UiO DUO archive; exact handle `UNVERIFIED`).
  4. Chris Christodoulou (2011). *Rumble in the Jungle: City, Place and Uncanny Bass*. Dancecult, 3(1), 44–63. DOI: <https://doi.org/10.12801/1947-5403.2011.03.01.03>
  5. Robert Ratcliffe (2014). *A Proposed Typology of Sampled Material within Electronic Dance Music*. Dancecult, 6(1), 97–122. DOI: <https://doi.org/10.12801/1947-5403.2014.06.01.06>
  6. Paul Harkins (2019). *Digital Sampling: The Design and Use of Music Technologies*. Routledge. (Exact ISBN `UNVERIFIED`).
  7. Chris Christodoulou (2020). *Bring the Break-Beat Back! Authenticity and the Politics of Rhythm in Drum 'n' Bass*. Dancecult, 12(1). DOI: <https://doi.org/10.12801/1947-5403.2020.12.01.08>
* **What it gives us:** Musicological & structural mechanics of target genres:
  * **Jungle / DnB (Noys 1995; Ratcliffe 2014; Christodoulou 2011, 2020; Harkins 2019):** How breakbeat chopping re-orders slice sequences, couples **pitch changes directly to playback speed/timestretch** (crucial for Ghostbreak's pitch-change modeling and Octatrack pitch/rate parameter locks!), creates snare-rush/retrig bursts, and contrasts hyper-syncopated double-time breaks (160–175 BPM) against half-time sub-bass.
  * **Techno / House / EDM (Butler 2006; Zeiner-Henriksen 2010):** Metrical ambiguity (turning the beat around), diatonic/asymmetric 3+3+2 / 3+3+3+3+4 rhythmic groupings in house/techno, and the vertical/timbral "poum-tchak" kick–offbeat-hi-hat oscillation.
* **Licence / Commercial & MIT compatibility:** *Dancecult* articles are Open Access; *Popular Music* and monographs are copyrighted texts (concepts freely usable).
* **Size:** Two monographs, one PhD thesis, four journal articles.
* **Quality:** Strong cultural/structural theory; less quantitative microtiming telemetry than MIR papers (particularly scarce on rominimal/UK techno microtiming—see Open Questions).
* **Recommendation:** **Use for domain-specific generative rules:** model pitch-coupled slice/hit playback, retrig rolls, half-time vs. double-time metrical phrasing, and asymmetric 3+3+2 accents directly in Ghostbreak's generator.

### 9. DAFx / ISMIR Drum-Transcription & Drum-Generation Line
* **Full Citations & URLs/DOIs:**
  1. Chih-Wei Wu, Christian Dittmar, Carl Southall, Richard Vogl, Gerhard Widmer, Jason Hockman, Meinard Müller, & Alexander Lerch (2018). *A Review of Automatic Drum Transcription*. IEEE/ACM TASLP, 26(9), 1457–1483. DOI: <https://doi.org/10.1109/TASLP.2018.2830113>
  2. Richard Vogl, Gerhard Widmer, & Peter Knees (2018). *Towards Multi-Instrument Drum Transcription*. DAFx-18. DOI: <https://doi.org/10.48550/arXiv.1806.06676>
  3. Lee Callender, Curtis Hawthorne, & Jesse Engel (2020). *Improving Perceptual Quality of Drum Transcription with the Expanded Groove MIDI Dataset*. arXiv:2004.00188. DOI: <https://doi.org/10.48550/arXiv.2004.00188>
  4. Fred Bruford, Olivier Lartillot, SKoT McDonald, & Mark B. Sandler (2020). *Multidimensional Similarity Modelling of Complex Drum Loops Using the GrooveToolbox*. ISMIR 2020. Repo: <https://github.com/fredbru/GrooveToolbox>
  5. Ondřej Cífka, Umut Şimşekli, & Gaël Richard (2020). *Groove2Groove: One-Shot Music Style Transfer with Supervision from Synthetic Data*. IEEE/ACM TASLP, 28, 2638–2650. DOI: <https://doi.org/10.1109/TASLP.2020.3019642>
  6. I-Chieh Wei, Chih-Wei Wu, & Li Su (2021). *Improving Automatic Drum Transcription Using Large-Scale Audio-to-MIDI Aligned Data*. IEEE ICASSP 2021. DOI: <https://doi.org/10.1109/ICASSP39728.2021.9414409>
  7. Mickaël Zehren, Marco Alunno, & Paolo Bientinesi (2021). *ADTOF: A Large Dataset of Non-Synthetic Music for Automatic Drum Transcription*. ISMIR 2021, pp. 818–824. <https://arxiv.org/abs/2111.11737>
  8. Behzad Haki, Marina Nieto, Teresa Pelinski, & Sergi Jordà (2022). *Real-Time Drum Accompaniment Using Transformer Architecture*. AIMC 2022. DOI: <https://doi.org/10.5281/zenodo.7088343>
  9. Francesco Foscarin, Jan Schlüter, & Gerhard Widmer (2024). *Beat This! Accurate Beat Tracking Without DBN Postprocessing*. ISMIR 2024. <https://arxiv.org/abs/2407.21658> | DOI: <https://doi.org/10.48550/arXiv.2407.21658>
  10. Alessandro Ilic Mezza, Riccardo Giampiccolo, Alberto Bernardini, & Augusto Sarti (2024). *Toward Deep Drum Source Separation* (LarsNet & StemGMD). Pattern Recognition Letters, 183, 86–91. DOI: <https://doi.org/10.1016/j.patrec.2024.04.026>
* **What it gives us:** Maps the complete engineering pipeline Ghostbreak needs: (1) beat/downbeat grid recovery without brittle DBN assumptions (*Beat This!* 2024), (2) drum stem isolation (*LarsNet* 2024) + multi-class ADT (*Wu et al.* 2018; *Vogl et al.* 2018; *ADTOF* 2021), (3) **velocity-preserving transcription** (*Callender et al.* 2020—proving standard onset F1 misses perceptual groove quality when dynamics are ignored!), (4) symbolic microtiming similarity features (*GrooveToolbox* 2020), and (5) lightweight transformer architectures for real-time HVO drum generation (*Haki et al.* 2022).
* **Licence / Commercial & MIT compatibility:** ISMIR/AIMC/arXiv papers are open access; IEEE/Elsevier papers are publisher-copyrighted (some have arXiv preprints). Companion code/weight licences vary widely—see Section (c).
* **Size:** 10 peer-reviewed conference/journal papers.
* **Quality:** State-of-the-art MIR/audio-ML canon.
* **Recommendation:** **Essential reading list.** Particularly prioritize Callender et al. (2020) for velocity transcription, Bruford et al. (2020) for groove feature vectors, Haki et al. (2022) for compact Transformer HVO generation, and Foscarin et al. (2024) for modern beat tracking.

### 10. Preference Learning & Preferential Bayesian Optimization for Generative Music
* **Full Citations & URLs/DOIs:**
  1. Wei Chu & Zoubin Ghahramani (2005). *Preference Learning with Gaussian Processes*. ICML 2005, pp. 137–144. DOI: <https://doi.org/10.1145/1102351.1102369>
  2. Javier González, Zhenwen Dai, Andreas Damianou, & Neil D. Lawrence (2017). *Preferential Bayesian Optimization*. ICML 2017, PMLR 70:1282–1291. <https://arxiv.org/abs/1704.03651>
  3. Yuki Koyama, Issei Sato, Daisuke Sakamoto, & Takeo Igarashi (2017). *Sequential Line Search for Efficient Visual Design Optimization by Crowds*. ACM Transactions on Graphics (SIGGRAPH 2017), 36(4). Project: <https://koyama.xyz/project/sequential_line_search/>
  4. Yuki Koyama, Issei Sato, & Masataka Goto (2020). *Sequential Gallery for Interactive Visual Design Optimization*. ACM Transactions on Graphics (SIGGRAPH 2020), 39(4). <https://arxiv.org/abs/2005.04107>
  5. Yijun Zhou, Yuki Koyama, Masataka Goto, & Takeo Igarashi (2021). *Interactive Exploration-Exploitation Balancing for Generative Melody Composition*. ACM IUI 2021. DOI: <https://doi.org/10.1145/3397481.3450663>
* **What it gives us:** Practical mathematics for adapting Ghostbreak from **very few user ratings** (10–50 interactions, not thousands): Gaussian Process preference models (Chu & Ghahramani 2005), Preferential BO with pairwise "A vs. B" comparisons or gallery picks (González et al. 2017; Koyama et al. 2017, 2020), and explicit user-controllable exploration–exploitation sliders over a music generator's latent/parameter space (Zhou et al. 2021). Directly avoids listener fatigue and scale drift common with 1–5 star ratings.
* **Licence / Commercial & MIT compatibility:** Academic methods freely implementable; see BoTorch and Koyama's MIT code in Section (c).
* **Size:** Five peer-reviewed ML/HCI papers.
* **Quality:** Proven sample efficiency for human-in-the-loop creative parameter tuning.
* **Recommendation:** **Adopt Preferential BO / Pairwise GP over low-dimensional groove/latent parameters** (e.g., 6–16D interpretable groove + pattern controls, or a compact VAE latent vector) rather than online deep RL, which requires far too many user ratings.

---

## (c) Tools and Libraries

### Drum Transcription & Drum Separation

#### 1. `librosa`
* **URL:** <https://github.com/librosa/librosa>
* **What it gives us:** Pure-Python audio analysis: STFT/mel/CQT, onset strength envelopes, onset detection (`onset_detect`), harmonic-percussive source separation (HPSS), dynamic-programming beat tracking (`beat_track`), spectral features (centroid, flatness, attack slope—useful for P-center estimation and classifying user one-shots).
* **Licence / Commercial & MIT compatibility:** **ISC License** (functionally equivalent to MIT). Fully MIT- and commercially compatible.
* **Size:** Lightweight Python package (~10–20 MB installed with deps like SciPy/Numba).
* **Quality:** Actively maintained, well-documented, deterministic, rock-solid baseline. Onset/beat trackers are DSP-based (weaker than neural trackers on complex syncopated jungle/breakbeat, strong on clean isolated hits).
* **Recommendation:** **Sensible core dependency (adopt first).** Use for I/O, one-shot transient/P-center analysis, and fast onset refinement.

#### 2. `Omnizart` (`Music-and-Culture-Technology-Lab/omnizart`)
* **URL:** <https://github.com/Music-and-Culture-Technology-Lab/omnizart>
* **What it gives us:** End-to-end Python music transcription toolkit including a pretrained drum transcription module outputting MIDI drum events from audio.
* **Licence / Commercial & MIT compatibility:** **MIT License** (repository). *Caveat:* exact licensing/provenance of every pretrained drum checkpoint (e.g., if trained on NC datasets like ENST/MDB/IDMT) is **`UNVERIFIED`**—verify checkpoint training-data terms before shipping pretrained weights commercially.
* **Size:** Medium Python package + TensorFlow runtime + pretrained checkpoints (~tens to hundreds of MB).
* **Quality:** Useful out-of-the-box prototype tool, but TensorFlow dependency stack is somewhat dated/heavy and accuracy trails newer PyTorch ADT models like ADTOF.
* **Recommendation:** **Reference / quick R&D baseline only** (do not lock Ghostbreak's production core to its TensorFlow stack without verifying model weight provenance).

#### 3. `ADTOF` & `ADTOF-pytorch`
* **URLs:** Official: <https://github.com/MZehren/ADTOF> | PyTorch port: <https://github.com/xavriley/ADTOF-pytorch>
* **What it gives us:** High-accuracy 5-class neural Automatic Drum Transcription (kick, snare, toms, hi-hat, cymbals) trained on 359 hours of real-world music. `ADTOF-pytorch` wraps inference cleanly in PyTorch.
* **Licence / Commercial & MIT compatibility:** **CC BY-NC-SA 4.0** (inherited from `MZehren/ADTOF`). **Incompatible** with MIT core or commercial distribution of pretrained weights.
* **Size:** Compact CRNN models (~tens of MB) + PyTorch/TensorFlow runtime.
* **Quality:** Very high real-world onset F1 across rock/pop/mixed audio; does **not** natively predict continuous hit velocity (outputs activation curves / binary peaks) and is less tested on heavily pitched/sliced jungle breaks.
* **Recommendation:** **Reference / non-commercial R&D benchmark only.** Do not bundle in a public MIT/commercial Ghostbreak release; use as an accuracy benchmark while training a clean CC BY 4.0 (E-GMD + StemGMD + FSL10K-CC0/CCBY) transcriber.

#### 4. `madmom` (`CPJKU/madmom`)
* **URL:** <https://github.com/CPJKU/madmom>
* **What it gives us:** Classic MIR audio signal processing & neural onset, beat, downbeat, and drum detection library (RNN + DBN trackers).
* **Licence / Commercial & MIT compatibility:** **Dual-licensed:** source code is **BSD**, but bundled **pretrained models and data files are CC BY-NC-SA 4.0** (non-commercial!).
* **Size:** Small Python/Cython package + compact neural weights.
* **Quality:** Historically gold-standard accuracy, but **maintenance is largely stalled** (frequent Cython / NumPy 2.x / Python 3.11+ build breakage in modern environments) and superseded in beat tracking by CPJKU's own `beat_this`.
* **Recommendation:** **Reference only.** Avoid as a dependency due to both NC model licensing and Python/NumPy maintenance friction.

#### 5. `Essentia` (`MTG/essentia`)
* **URL:** <https://github.com/MTG/essentia>
* **What it gives us:** High-performance C++/Python audio feature extraction library: onset detection, rhythm/BPM/beat extractors (`RhythmExtractor2013`, `BeatTrackerDegara`, TensorFlow-backed `TempoCNN`), loudness, and spectral/timbral descriptors.
* **Licence / Commercial & MIT compatibility:** **AGPL-3.0** (strong network copyleft; commercial proprietary licences available from MTG/UPF for a fee). **Incompatible** with an MIT-licensed Ghostbreak release.
* **Size:** Medium C++ library + Python wheels (~50–150 MB with optional TF models).
* **Quality:** Production-grade DSP performance and huge descriptor catalog.
* **Recommendation:** **Reference only** (unless Ghostbreak switches to AGPL-3.0 or buys a commercial licence). Re-implement any needed DSP descriptors cleanly in NumPy/SciPy/`librosa`.

#### 6. `LarsNet` (`polimi-ispl/larsnet`) & `Demucs` (`facebookresearch/demucs`)
* **URLs:** LarsNet: <https://github.com/polimi-ispl/larsnet> | Demucs: <https://github.com/facebookresearch/demucs>
* **What it gives us:**
  * **LarsNet:** Separates a stereo drum loop into 5 drum stems (kick, snare, toms, hi-hat, cymbals)—very attractive for extracting per-voice microtiming, velocity, and pitch envelopes from dense breakbeats!
  * **Demucs (HTDemucs):** Separates full music tracks into 4 stems (drums, bass, vocals, other), useful if a user feeds a full track rather than an isolated drum break.
* **Licence / Commercial & MIT compatibility:**
  * **LarsNet:** **No GitHub root `LICENSE` file** on `polimi-ispl/larsnet` (verified via GitHub API; defaults to All Rights Reserved); **pretrained weights are CC BY-NC 4.0** (non-commercial). The underlying **StemGMD** dataset is **CC BY 4.0**.
  * **Demucs:** Source code is **MIT License**; verify pretrained weight commercial terms (standard HTDemucs weights were trained partly on MUSDB18 + internal/extra data; check Meta's model card/terms carefully—flag commercial weight redistribution as **CAUTION**).
* **Size:** LarsNet: 5 parallel U-Nets (~hundreds of MB); Demucs HTDemucs checkpoints ~80–300 MB.
* **Quality:** Both are top-tier in their respective domains; LarsNet is trained on acoustic kits (StemGMD), so electronic/pitched DnB breaks may need fine-tuning.
* **Recommendation:** **Architecture reference (LarsNet) / optional local user-invoked pre-processor (Demucs).** Do not bundle CC BY-NC LarsNet weights in a commercial MIT app; retraining LarsNet's architecture on CC BY 4.0 StemGMD + synthetic electronic/break mixes is a clean Phase 2 path.

### Beat & Downbeat Tracking

#### 7. `beat_this` (`CPJKU/beat_this`)
* **URL:** <https://github.com/CPJKU/beat_this> (paper: <https://arxiv.org/abs/2407.21658>)
* **What it gives us:** State-of-the-art PyTorch beat and downbeat tracker (ISMIR 2024) that drops madmom's brittle DBN post-processing in favor of a modern neural architecture, handling tempo changes and non-4/4 or complex syncopated rhythms far better than classical trackers.
* **Licence / Commercial & MIT compatibility:** Code is **MIT License**. *Important caveat:* CPJKU notes pretrained checkpoints are provided for research purposes because underlying training corpora include copyrighted third-party music datasets—verify before shipping pretrained weights in a commercial product.
* **Size:** Compact PyTorch model (~tens of MB) + PyTorch runtime.
* **Quality:** Actively maintained (2024+), modern Python/PyTorch stack, state-of-the-art beat/downbeat F1.
* **Recommendation:** **Sensible dependency for R&D and local analysis (adopt first for beat/downbeat grid extraction)**; verify checkpoint licensing before any commercial redistribution.

#### 8. `BeatNet` (`mjhydri/BeatNet`) & `allin1` (`mir-aidj/all-in-one`)
* **URLs:** BeatNet: <https://github.com/mjhydri/BeatNet> | allin1: <https://github.com/mir-aidj/all-in-one>
* **What it gives us:**
  * **BeatNet:** Real-time and offline joint beat, downbeat, tempo, and meter tracking.
  * **allin1:** Joint beat, downbeat, BPM, and functional song-structure segmentation (intro/drop/break/etc.).
* **Licence / Commercial & MIT compatibility:** BeatNet is **CC BY 4.0** (note: CC licenses are non-ideal for software code, though permissive with attribution; also depends on `madmom` in some modes—watch out for `madmom` build/NC-model issues!). `allin1` is **MIT License** (uses Demucs + NATTEN under the hood—check sub-dependency/weight terms).
* **Size:** Medium PyTorch packages + weights (~tens to hundreds of MB).
* **Quality:** Solid MIR tools, though `beat_this` is cleaner and lighter when Ghostbreak only needs beat/downbeat grids on short drum breaks.
* **Recommendation:** **Defer / reference only.** Prefer `beat_this` + `librosa` for short break alignment.

### Groove Templates, Symbolic MIDI, & Octatrack Formats

#### 9. Ableton Live `.agr` Groove Format & Built-In Groove Pool
* **URL:** Ableton Live manual reference: <https://www.ableton.com/en/manual/using-grooves/>
* **What it gives us:** Ableton's Groove Pool concept—parameterizing groove extraction and application via four intuitive controls (**Base grid**, **Quantize %**, **Timing %**, **Random %**, **Velocity %**) applied non-destructively to clips. Unlike `.als`/`.adg`/`.alc` files (which are gzip-compressed XML), **`.agr` groove files are a proprietary undocumented binary format** with no mainstream open-source parser.
* **Licence / Commercial & MIT compatibility:** **Proprietary / Copyrighted** (Ableton AG). Never ship or convert Ableton's bundled `.agr` groove library inside Ghostbreak.
* **Size:** Individual `.agr` files are tiny (KB), but proprietary.
* **Quality:** UX gold standard for producer-facing groove controls (Timing %, Velocity %, Random %, Base).
* **Recommendation:** **UX & conceptual reference only.** Do **not** depend on `.agr` files; use standard MIDI (`.mid`) or an open JSON HVO groove template schema in Ghostbreak, while adopting Ableton's clean Timing/Velocity/Quantize/Random control paradigm.

#### 10. Akai MPC Swing (Roger Linn Algorithm)
* **URL:** Primary interview / explanation: <https://www.attackmagazine.com/features/interviews/roger-inn-on-swing-groove-the-magic-of-the-mpcs-timing/> *(note: `roger-inn` is Attack Magazine's actual URL slug)*
* **What it gives us:** Deterministic, zero-dependency swing model: operates on quantized 16th-note pairs within each 8th-note window, keeping odd 16ths (1, 3, 5, 7...) fixed and delaying even 16ths (2, 4, 6, 8...) by $S \in [50\%, 75\%]$ of the 8th-note duration (50% = straight, 66.7% = triplet, 75% = dotted-16th). On classic MPC60/MPC3000 hardware (96 PPQN = 48 ticks per 8th note, 24 ticks per 16th note), the even 16th lands at $\text{round}(48 \times S)$ ticks—the coarse discrete quantization onto 96 PPQN ticks contributes to the recognizable hardware "pocket."
* **Licence / Commercial & MIT compatibility:** **Mathematical algorithm / unencumbered.** Fully compatible with MIT/commercial Ghostbreak.
* **Size:** ~10 lines of pure code.
* **Quality:** Directly maps to house, hip-hop, UK garage/bass, and minimal techno swing—and translates cleanly onto Elektron Octatrack microtiming ticks (1/384th bar = 24 micro-ticks per 16th-note step, identical to 96 PPQN!).
* **Recommendation:** **Sensible built-in primitive (adopt first).** Include parametric MPC-style 16th/8th swing (including optional 96 PPQN / Octatrack 1/384-bar grid quantization) as a baseline groove generator and prior.

#### 11. `GrooveToolbox` (`fredbru/GrooveToolbox`) & `pretty_midi` (`craffel/pretty-midi`)
* **URLs:** GrooveToolbox: <https://github.com/fredbru/GrooveToolbox> | pretty_midi: <https://github.com/craffel/pretty-midi>
* **What it gives us:**
  * **GrooveToolbox:** Python library (ISMIR 2020) computing symbolic drum-loop features, perceptual similarity metrics, and four specialized microtiming descriptors.
  * **pretty_midi:** Clean, standard Python MIDI parsing, timing manipulation, tempo-map handling, and drum-track I/O.
* **Licence / Commercial & MIT compatibility:** GrooveToolbox is **Apache-2.0**; `pretty_midi` is **MIT License**. Both are fully MIT- and commercially compatible.
* **Size:** Small pure-Python libraries (<10 MB).
* **Quality:** `pretty_midi` is mature and ubiquitous; `GrooveToolbox` is research code (ISMIR 2020)—check Python 3.11+ compatibility, but its feature math is clean and easy to vendor/adapt under Apache-2.0.
* **Recommendation:** **Sensible dependencies (adopt first).** Use `pretty_midi` for MIDI I/O and `GrooveToolbox` (or extracted Apache-2.0 microtiming/syncopation descriptors) to build the feature space for Ghostbreak's preference model!

#### 12. Open Algorithmic Pattern & Octatrack Format Tools (`strudel`, `ot-tools` / `ot-tools-io`)
* **URLs:** Strudel: <https://github.com/tidalcycles/strudel> | `ot-tools` (dijksterhuis): search/crates ecosystem (exact GitHub repo URL `UNVERIFIED` — confirmed GPL-3.0 Rust libraries/CLI for reading/writing Elektron Octatrack `.ot`/project files).
* **What it gives us:**
  * **Strudel:** Browser-based TidalCycles port with expressive cycle-based polyrhythms, Euclidean patterns, and break-slicing notation.
  * **`ot-tools` / `ot-tools-io`:** Community reverse-engineered Rust crates for Elektron Octatrack binary project/bank/arrangement/sample-settings (`.ot`) files, including microtiming trig offsets (range `-23/384` to `+23/384` of a bar, i.e., `-23/24` to `+23/24` of a 16th-note step).
* **Licence / Commercial & MIT compatibility:** **Strudel is AGPL-3.0**; **`ot-tools` / `ot-tools-io` is GPL-3.0**. **Both are incompatible as embedded dependencies in an MIT-licensed Ghostbreak core!**
* **Size:** Lightweight JS/Rust toolkits.
* **Quality:** Very high domain relevance for Octatrack export, but strong copyleft restricts direct linking into an MIT tool.
* **Recommendation:** **Reference only (for an MIT Ghostbreak)**—or keep Ghostbreak's Octatrack exporter as a clean-room MIT implementation of the binary format (or evaluate whether Ghostbreak itself should be GPL-3.0 if direct reuse of `ot-tools-io` is desired).

### Open Drum-Generation Models

#### 13. Magenta `GrooVAE` & `DrumsRNN` (`magenta/magenta`, `magenta/magenta-js`)
* **URLs:** <https://github.com/magenta/magenta> | <https://github.com/magenta/magenta-js>
* **What it gives us:** Classic symbolic drum generators: `DrumsRNN` (LSTM quantized drum pattern generation) and `GrooVAE` (VAE humanizer + Tap2Drum over HVO tensors trained on GMD).
* **Licence / Commercial & MIT compatibility:** **Apache-2.0** (code) + **CC BY 4.0** (GMD training data). Fully MIT/commercial compatible.
* **Size:** Small symbolic models (a few MB for checkpoints), but heavy legacy TensorFlow runtime in Python.
* **Quality:** **Archived / low active maintenance**; legacy TensorFlow dependencies are painful in modern Python environments. Conceptually clean and still easy to re-implement in ~200 lines of modern PyTorch!
* **Recommendation:** **Architecture & algorithm reference only** (do not depend on the `magenta` Python package). Re-implement a clean PyTorch HVO-VAE / small Transformer trained on CC BY 4.0 GMD instead.

#### 14. Behzad Haki's `MonotonicGrooveTransformer` & `GrooveTransformer`
* **URLs:** <https://github.com/behzadhaki/MonotonicGrooveTransformer> | <https://github.com/behzadhaki/GrooveTransformer>
* **What it gives us:** Compact real-time Transformer architectures operating directly on `HVO_Sequence` drum representations (Tap2Drum / accompaniment / variational groove generation) plus `GrooveEvaluator` metrics and a Camomile/Pure Data VST3 prototype.
* **Licence / Commercial & MIT compatibility:** **NO LICENSE FILE on GitHub (= All Rights Reserved by default).** **Incompatible** as a code dependency or copy-paste source for an MIT/commercial Ghostbreak unless the author grants an explicit licence.
* **Size:** Lightweight PyTorch research repos (a few MB).
* **Quality:** Strong modern PyTorch symbolic groove design (2022–2024); static research artifacts.
* **Recommendation:** **Paper/architecture reference only** (strictly do not copy code unless an open licence is added). Read the AIMC 2022 paper (DOI `10.5281/zenodo.7088343`) and implement Ghostbreak's own MIT PyTorch Transformer from scratch.

#### 15. `Groove2Groove` (`cifkao/groove2groove`)
* **URL:** <https://github.com/cifkao/groove2groove>
* **What it gives us:** One-shot symbolic music style transfer (IEEE/ACM TASLP 2020) separating content from rhythmic/accompaniment style.
* **Licence / Commercial & MIT compatibility:** **BSD-3-Clause License**. Fully compatible with MIT/commercial use (note: relies on Band-in-a-Box synthetic data via `pybiab` for full training).
* **Size:** Medium research codebase + TensorFlow model.
* **Quality:** Interesting disentanglement ideas for transferring groove style across patterns, though focused on full accompaniment rather than breakbeat drum synthesis.
* **Recommendation:** **Reference only** (for content-vs-groove disentanglement ideas).

#### 16. Audio-Domain Drum / Music Generators (`DrumGAN`, `CRASH`, `VampNet`, `AudioCraft`)
* **URLs:**
  * DrumGAN: <https://github.com/SonyCSLParis/DrumGAN>
  * CRASH: <https://github.com/simonrouard/crash>
  * VampNet: <https://github.com/hugofloresgarcia/vampnet>
  * AudioCraft / MusicGen: <https://github.com/facebookresearch/audiocraft>
* **What it gives us:**
  * **DrumGAN / CRASH:** Raw-audio one-shot drum hit synthesis (GAN / diffusion). Not Ghostbreak's core mission (Ghostbreak uses the **user's own one-shot samples**!), though relevant if Ghostbreak ever adds synthetic layer/variation generation.
  * **VampNet / AudioCraft:** Masked-token / autoregressive raw-audio music/loop generation. Heavy GPU footprint and does not output Octatrack step trigs or use user one-shots natively.
* **Licence / Commercial & MIT compatibility:**
  * **VampNet:** Code **MIT License** (verify pretrained weight training-data terms before commercial use).
  * **AudioCraft:** Code **MIT License**, **pretrained weights are CC BY-NC 4.0** (non-commercial!).
  * **DrumGAN / CRASH:** `SonyCSLParis/DrumGAN` has a custom non-standard `LICENSE` (`NOASSERTION` via GitHub API; training data is private); `simonrouard/crash` has **no GitHub `LICENSE` file** (= All Rights Reserved by default). Both ISMIR papers are CC BY 4.0.
* **Size:** Hundreds of MB to multi-GB models; GPU required for comfortable generation.
* **Quality:** High audio quality, poor fit for sample-based Octatrack symbolic/trig generation.
* **Recommendation:** **Defer / out of scope for v1.** Ghostbreak's core value is *symbolic + microtiming/velocity/pitch* break generation over the user's own one-shots exported to Octatrack—raw audio neural synthesis works against that workflow.

### Preference Learning, Bayesian Optimization, & Bandits for User Ratings

#### 17. `BoTorch` (`meta-pytorch/botorch`) — `PairwiseGP`
* **URL:** <https://github.com/meta-pytorch/botorch> (Preference BO tutorial: <https://github.com/meta-pytorch/botorch/tree/main/tutorials/preference_bo>)
* **What it gives us:** Production-grade PyTorch Bayesian Optimization library with first-class **Preferential Bayesian Optimization**: `PairwiseGP`, `PairwiseLaplaceMarginalLogLikelihood`, and acquisition functions (`qExpectedImprovement`, `AnalyticExpectedUtilityOfBestOption` / EUBO) designed to learn a user's latent taste from pairwise preferences ("Break A > Break B") or ranked batches.
* **Licence / Commercial & MIT compatibility:** **MIT License**. Fully compatible with MIT/commercial Ghostbreak.
* **Size:** Lightweight Python library built on PyTorch + GPyTorch.
* **Quality:** Actively maintained by Meta PyTorch ecosystem; gold standard for sample-efficient preference learning in low-to-moderate dimensions (e.g., 5–20D groove/pattern control vectors or compact VAE latent spaces).
* **Recommendation:** **Sensible core dependency (adopt first for preference adaptation).** Pairwise or small-gallery comparisons (pick 1–2 favorites out of 4 generated breaks) using `PairwiseGP` will converge dramatically faster and more reliably than asking producers for absolute 1–5 star ratings.

#### 18. `Optuna` (`optuna/optuna`) & Koyama's `sequential-line-search`
* **URLs:** Optuna: <https://github.com/optuna/optuna> | Sequential Line Search: <https://github.com/yuki-koyama/sequential-line-search>
* **What it gives us:**
  * **Optuna:** Clean, lightweight black-box hyperparameter optimization (TPE, CMA-ES, GP samplers) for scalar ratings (e.g., if Ghostbreak initially uses simple 1–5 star or thumbs-up/down scores without PyTorch).
  * **`sequential-line-search`:** Reference C++/Python implementation of Koyama et al.'s (SIGGRAPH 2017/2020) 1D slider / 2D gallery preferential BO for creative design tools.
* **Licence / Commercial & MIT compatibility:** Both **MIT License**. Fully MIT/commercial compatible.
* **Size:** Very lightweight (Optuna requires no PyTorch!).
* **Quality:** Optuna is rock-solid and mature; `sequential-line-search` is clean research code ideal for UX inspiration.
* **Recommendation:** **Optuna = sensible lightweight fallback** if Ghostbreak v1 avoids PyTorch for scalar ratings; **`sequential-line-search` = UX & algorithmic reference** for building an intuitive "pick the grooviest break from 4 candidates" or "slider morph" interface.

#### 19. Contextual / Dueling Bandits (Lightweight Pure-NumPy Alternative)
* **URL:** Reference foundation: Chu & Ghahramani (2005) <https://doi.org/10.1145/1102351.1102369>; González et al. (2017) <https://arxiv.org/abs/1704.03651>
* **What it gives us:** A linear/kernel **Bradley-Terry** or **Thompson-Sampling contextual bandit** over explicit interpretable rhythm features (computed via `GrooveToolbox` / custom descriptors: syncopation index, kick density, ghost-snare density, 16th swing ratio, snare pocket offset, hat velocity variance, pitch-variation rate, retrig probability).
* **Licence / Commercial & MIT compatibility:** Pure math / custom MIT implementation (~100 lines of NumPy/SciPy). Zero third-party licensing or heavy runtime burden.
* **Size:** Negligible (<10 KB code; sub-millisecond updates on CPU).
* **Quality:** Extremely transparent, debuggable, and cold-start friendly—can be serialized per user or per genre profile in a tiny JSON file.
* **Recommendation:** **Adopt first for Prototype v0.1** (before escalating to BoTorch `PairwiseGP` in v0.2), because a Bayesian linear Bradley-Terry / Thompson-sampling model over ~12–16 interpretable groove features works immediately on CPU with zero GPU or PyTorch overhead.

---

## (d) Genre-Specific Rhythm Knowledge Sources & Copyright Flags

### 1. Provenance of Classic Breaks Used as Rhythmic References

| Classic Break | Source Track & Artist | Year | Drummer / Performer(s) | Original Label | Rhythmic / Genre Significance |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Amen Break** | *"Amen, Brother"* — The Winstons (B-side to *"Color Him Father"*) | 1969 | Gregory C. Coleman (arr. Richard L. Spencer) | Metromedia Records | ~6–7 s, 4-bar solo; defining rhythmic DNA of jungle, DnB, breakcore, and UK hardcore (syncopated kick-snare phrasing, bar-3/bar-4 crash & shifted snare turnaround, pitched/chopped slicing). |
| **Think Break** | *"Think (About It)"* — Lyn Collins (prod. James Brown) | 1972 | John "Jabo" Starks | People Records | Crisp, driving 16th-note tambourine/shaker-laced break with vocal shouts ("yeah! woo!"); ubiquitous in jungle, DnB, breakbeat, and house layering (high-frequency shuffle energy). |
| **Apache Break** | *"Apache"* — The Incredible Bongo Band (comp. Jerry Lordan, 1960) | 1973 | Jim Gordon (drums), King Errisson (bongos/congas) | Pride Records / MGM | Extended percussion + drum-kit break; foundational in hip-hop, breakbeat, and jungle/DnB (bongo-driven syncopation and tom fills). |
| **Funky Drummer** | *"Funky Drummer"* — James Brown | 1970 | Clyde Stubblefield | King Records | Archetypal 16th-note funk pocket with intricate ghost snares and open/closed hi-hat interplay; blueprint for hip-hop, big beat, breakbeat, and liquid/classic DnB. |
| **Hot Pants** | *"Hot Pants… I'm Coming, I'm Coming, I'm Coming"* — Bobby Byrd (companion to James Brown's *"Hot Pants"*) | 1971 | John "Jabo" Starks *(credited on James Brown's related 1971 session; exact Bobby Byrd Bonus Beats session drummer details vary across discographies—treat specific take credit carefully)* | People / Polydor *(exact original 7" vs. later Bonus Beats reissue label details `UNVERIFIED`)* | Bright, tight, high-energy snare/hi-hat/tambourine shuffle ("Hot Pants Bonus Beats"); staple top-loop layer in DnB and jungle. |
| **Impeach the President** | *"Impeach the President"* — The Honey Drippers | 1973 | "Morris" (last name uncredited/obscure; prod. Roy C. Hammond) | Alaga Records *(label `UNVERIFIED`)* | Iconic open hi-hat + punchy kick-snare 2-bar intro; cornerstone of boom-bap, breakbeat, and swung house/UK bass crossovers. |
| **Synthetic Substitution** | *"Synthetic Substitution"* — Melvin Bliss | 1973 | Bernard "Pretty" Purdie | Sunburst Records *(label `UNVERIFIED`)* | Masterclass in Purdie ghost notes, double-kick pickup, and crisp hi-hat syncopation; widely referenced in hip-hop and breakbeat. |
| **It's a New Day** | *"It's a New Day"* — Skull Snaps | 1973 | George "Buzzy" Bragg | GSF Records *(label `UNVERIFIED`)* | Razor-sharp, dry, punchy 2-bar intro break; iconic boom-bap and modern breakbeat/DnB transient reference. |

### 2. Historical & Reference Knowledge Sources (Metadata & Cultural Lineage)

#### a. *Ultimate Breaks and Beats* (Street Beat Records, 1986–1991)
* **URL:** Discogs reference index: <https://www.discogs.com/label/29770-Street-Beat-Records> (historical overview via Stones Throw / Discogs; exact catalog URL `UNVERIFIED`)
* **What it gives us:** The 25-volume vinyl compilation series curated by Lenny "BreakBeat Lenny" Roberts and edited by Louis "BreakBeat Lou" Flores that codified the canonical breakbeat repertoire for hip-hop, hardcore, jungle, and DnB producers.
* **Licence / Commercial & MIT compatibility:** **Copyrighted commercial/bootleg-era sound recordings**—strictly a **historical discography reference**, never an audio source.
* **Size:** 25 compilation volumes (hundreds of classic funk/soul/rock tracks).
* **Quality:** Authoritative historical provenance map of the breakbeat canon.
* **Recommendation:** **Historical reference only.** Use to understand genre lineage and drum-kit instrumentation conventions.

#### b. Nate Harrison (2004) — *"Can I Get an Amen?"*
* **URL:** Historical documentation widely archived online (exact official artist archive URL `UNVERIFIED`; verified 2004 18-minute audio documentary/installation by Nate Harrison).
* **What it gives us:** Accessible, widely cited historical/legal documentary tracing the Amen Break from The Winstons (1969) through *Ultimate Breaks and Beats*, N.W.A, UK jungle/DnB, and copyright paradoxes.
* **Licence / Commercial & MIT compatibility:** Copyrighted artwork/documentary (reference only).
* **Size:** ~18-minute audio documentary.
* **Quality:** Accurate high-level cultural/historical primer (supplement with statutory texts below for post-2018 copyright accuracy, since the 2018 Music Modernization Act changed U.S. pre-1972 sound recording law long after 2004).
* **Recommendation:** **Background context only.**

#### c. Genre-Specific Rhythm Knowledge Beyond Breaks (House, Minimal, Dub Techno, Rominimal, UK Techno / UK Bass)
* **URLs:** Mark J. Butler (2006) ISBN `978-0-253-21804-9`; Brøvig-Hanssen et al. (2022) <https://doi.org/10.1093/mts/mtab013>; Brøvig-Hanssen et al. (2020) *MTO* 26(2); Zeiner-Henriksen (2010); GiantSteps Drum Patterns <https://github.com/GiantSteps/drum-pattern-datasets>.
* **What it gives us:** Structural and micro-rhythmic conventions across Ghostbreak's non-breakbeat club genres:
  * **House (118–130 BPM):** Four-on-the-floor kick anchored tightly on the grid, 16th-note MPC-style swing (often 54%–62%) on hi-hats/shakers/percussion, clap/snare slightly nudged around beats 2 & 4, velocity-driven "poum-tchak" bounce.
  * **Minimal / Rominimal (122–130 BPM):** Hypnotic, sparse micro-percussion loops; long-cycle (4–16 bar) subtle velocity, filter, pitch, and microtiming modulations; off-grid ghost clicks/rims and shifting P-centers via envelope/decay changes (note: peer-reviewed MIR microtiming measurements specifically on *rominimal* are largely absent—see Open Questions).
  * **Dub Techno (115–128 BPM):** Steady kick paired with tape-delay / dotted-8th (3/16) echo rhythms, wide beat bins created by reverberant/filtered transients (Danielsen 2010; Brøvig-Hanssen et al. 2020, 2022), and subtle swing on sparse hi-hat/chord stabs.
  * **UK Techno / UK Bass-adjacent (125–140 BPM):** Hybrids fusing four-on-the-floor or broken-beat/2-step/UKG syncopation (3+3+2 / 3+3+4 kick placements, swung 16ths around 58%–66%, pitched tom/percussion accents, chopped break ghosts layered over electronic kicks).
  * **Jungle / DnB (160–178 BPM):** Double-time break architecture with syncopated kick-snare interplay (classic two-step vs. chopped Amen/Think/Hot Pants phrases), pitch-shifted hits (often +2 to +7 semitones when sped up), snare rushes/retrigs, ghost-note velocity ladders, and compressed swing ratios due to high tempo (consistent with Friberg & Sundström 2002).
* **Licence / Commercial & MIT compatibility:** Musical conventions, genre rules, and abstract timing/accent parameters are **not copyrightable**. Fully safe for Ghostbreak's MIT generator presets.
* **Size:** Cross-genre synthesis of academic musicology + MIR literature.
* **Quality:** Strong scholarly foundation for house/techno/DnB; empirical MIR data for rominimal and UK bass-adjacent microtiming is sparse (`UNVERIFIED` quantitatively).
* **Recommendation:** **Encode as interpretable parametric priors** (tempo range, swing curve, voice-pocket offsets, syncopation density, pitch/retrig envelopes) that users can steer via ratings or override by analyzing their own reference breaks.

---

### 3. Copyright Status & Compliance Flags for Ghostbreak *(Not Legal Advice)*

> **Disclaimer:** The notes below are factual copyright-status flags grounded in primary statutory and judicial sources to guide engineering boundaries. They are **not legal advice**; consult qualified intellectual-property counsel before any commercial release.

#### Why Classic Breaks (1967–1975) Are Still Protected
1. **Dual copyrights:** Every commercial break recording involves two separate rights: (a) the **sound recording (master)** copyright, and (b) any copyrightable **musical composition** (for example, *Apache* is a 1960 composition by Jerry Lordan, separate from the Incredible Bongo Band's 1973 Pride/MGM master recording).
2. **United States — Pre-1972 Sound Recordings Are Protected Until 2067:** Under Title II of the 2018 Music Modernization Act (**Classics Protection and Access Act**, **17 U.S.C. § 1401**, <https://www.copyright.gov/music-modernization/pre1972-soundrecordings/>), sound recordings first published before February 15, 1972 now receive federal remedies. Recordings published between 1957 and February 14, 1972 (which includes *Amen, Brother* 1969, *Funky Drummer* 1970, and *Hot Pants* 1971) remain protected through **February 15, 2067**. Post-February 15, 1972 recordings (such as *Think (About It)* 1972, *Apache* 1973, *Impeach the President* 1973, *Synthetic Substitution* 1973, *It's a New Day* 1973) fall under federal copyright (typically 95 years from publication for corporate works-for-hire, i.e., **2067–2068+**).
3. **European Union & UK — 70-Year Sound Recording Term:** Under **Directive 2011/77/EU** (amending Directive 2006/116/EC), phonogram producers' and performers' related rights run for **70 years from lawful publication** (so 1969–1973 breaks remain protected until at least **2039–2043**).
4. **Strict Audio Sampling Case Law:**
   * **US Circuit Split:** The Sixth Circuit (*Bridgeport Music, Inc. v. Dimension Films*, 410 F.3d 792, 6th Cir. 2005) rejected any *de minimis* defense for sound recording sampling ("Get a license or do not sample"), while the Ninth Circuit (*VMG Salsoul, LLC v. Ciccone*, 824 F.3d 871, 9th Cir. 2016) allowed *de minimis* for an unrecognizable 0.23-second fragment. The Supreme Court has not resolved the split.
   * **EU Law:** In *Pelham v. Hütter* (**CJEU Case C-476/17**, July 29, 2019), the Court of Justice of the European Union held that reproducing even a very short (~2-second) recognizable rhythmic sample from a phonogram without authorization infringes the phonogram producer's reproduction right under Directive 2001/29/EC, unless transformed into an unrecognizable new form or covered by a statutory exception (e.g., pastiche/quotation).

#### Clear Engineering Red Lines for Ghostbreak
* **HARD RED LINE — Do NOT ship, bundle, or redistribute copyrighted breaks:** Ghostbreak must **never** bundle Amen, Think, Apache, Funky Drummer, commercial sample packs, or Ableton `.agr` files in its repository, installers, demos, or test fixtures.
* **HARD RED LINE — Do NOT train publicly released model weights on copyrighted breaks:** Training a distributed ML model on unlicensed commercial recordings creates unresolved copyright/memorization liability across US and EU jurisdictions. Train any shipped Ghostbreak weights **exclusively** on clearly licensed data (**CC0 / CC BY 4.0**: GMD, E-GMD, StemGMD, CC0/CC-BY subset of FSL10K, or newly recorded CC0 drum breaks commissioned/created for Ghostbreak).
* **DISTINCTION — Local user-side analysis of user-owned files:** Allowing a user to run Ghostbreak **locally** on a legally obtained audio file the user already owns to extract **abstract, non-audio rhythmic parameters** (beat grid, 16th-note swing ratio, per-voice millisecond microtiming offsets, relative velocity/accent contours, pitch-shift curve) and then rendering a **brand-new break using the user's own one-shot samples** is fundamentally different from copying, redistributing, or sampling the source phonogram—no audio from the reference recording is copied or exported. Still, Ghostbreak should:
  1. Never cache or redistribute source audio outside the user's local machine.
  2. Never phone home or upload user reference audio or extracted commercial-track fingerprints to a shared training server.
  3. Keep shipped built-in groove presets derived solely from **CC0 / CC BY 4.0 datasets** or **purely parametric/algorithmic models** (e.g., MPC swing, Danielsen/Butler/Frane-informed parameter ranges).

---

## Ranked Shortlist for a First Prototype

### Tier 1 — Adopt First (Prototype v0.1 / v0.2 Core Stack — 100% MIT / Apache-2.0 / ISC / CC BY 4.0 Clean)

1. **HVO Symbolic Groove Schema (from Gillick et al. / GrooVAE, ICML 2019) + Octatrack Mapping**
   * *Why first:* Maps cleanly to Elektron Octatrack step trigs (hit -> trig, velocity -> volume/velocity p-lock, timing offset -> `-23/384` to `+23/384` trig microtiming, plus pitch/retrig/sample-slot p-locks). Zero external dependency.
2. **Groove MIDI Dataset (GMD, CC BY 4.0) + E-GMD (CC BY 4.0)**
   * *Why first:* Commercially safe, MIT-compatible, high-resolution human microtiming + velocity ground truth. Use GMD to seed statistical/generative groove priors and E-GMD to validate or train velocity-aware transcription.
3. **`librosa` (ISC) + `pretty_midi` (MIT) + `GrooveToolbox` descriptors (Apache-2.0)**
   * *Why first:* Lightweight, clean-licence Python core for audio loading, one-shot P-center / attack-centroid analysis (Danielsen et al. 2019), MIDI I/O, and interpretable groove feature vectors (swing, syncopation, microtiming offsets, velocity contours).
4. **Parametric Groove Primitives (Roger Linn MPC Swing + Friberg & Sundström Tempo-Dependent Swing Scaling + Butterfield Inter-Voice Pocket + P-Center Compensation)**
   * *Why first:* Gives immediate, musical control across house, minimal/dub techno, rominimal, UK techno, and breakbeat/DnB *before* any heavy neural model is trained—and produces an interpretable 10–16D parameter space for user preference learning.
5. **Preference Learning: Lightweight Bayesian Bradley-Terry / Thompson Sampling (v0.1) -> `BoTorch` `PairwiseGP` (v0.2, MIT)**
   * *Why first:* Pairwise / small-gallery comparisons ("pick your favorite of 4 fresh breaks") converge in 10–30 user interactions without listener fatigue (González et al. 2017; Koyama et al. 2020; Zhou et al. 2021). Start over interpretable groove + pattern parameters before adding neural latent spaces.
6. **`beat_this` (MIT code; verify checkpoint terms before commercial shipping) + FSL10K CC0/CC-BY subset**
   * *Why first:* Fast, accurate beat/downbeat grid extraction on user-supplied reference breaks (tested against legally clean CC0/CC-BY electronic/breakbeat loops from FSL10K).

### Tier 2 — Adopt Second (Prototype v0.3 / Phase 2)

1. **Custom PyTorch HVO-VAE / Compact Groove Transformer (clean-room MIT implementation inspired by Gillick et al. 2019 & Haki et al. 2022)**
   * *Why defer to v0.3:* Parametric/probabilistic break + groove generation is faster to debug and steer with user ratings in v0.1–v0.2; once the Octatrack exporter and preference loop work end-to-end, train a clean MIT PyTorch HVO model on GMD + CC0 breakbeat/EDM patterns.
2. **Clean-Licence Velocity-Aware Drum Transcriber & Drum Separator (trained on E-GMD + StemGMD, CC BY 4.0)**
   * *Why defer to v0.3:* Avoids the CC BY-NC licensing traps of ADTOF, madmom, and pretrained LarsNet weights while giving Ghostbreak commercial-grade per-voice onset + velocity extraction from dense user breaks.
3. **Swingogram Bar-by-Bar Micro-Rhythmic Tracking (Dittmar et al., 2018)**
   * *Why defer:* Valuable for capturing multi-bar groove drift in live funk breaks and hypnotic rominimal/dub-techno loops after basic 1–2 bar groove extraction works.
4. **GiantSteps Drum Pattern Datasets (after verifying repo LICENSE)**
   * *Why defer:* Nice lightweight symbolic genre prior for house/techno step patterns once the core generator is running.

### Tier 3 — Defer / Reference Only (Do Not Adopt as Production Dependencies)

* **Essentia (AGPL-3.0), `ot-tools` / `ot-tools-io` (GPL-3.0), Strudel (AGPL-3.0):** Strong copyleft blockers for an MIT core (note: for Octatrack project export, either write a clean-room MIT exporter or reconsider Ghostbreak's own licence).
* **ADTOF / `ADTOF-pytorch` (CC BY-NC-SA 4.0), `madmom` pretrained models (CC BY-NC-SA 4.0 + stalled maintenance), `LarsNet` pretrained weights (CC BY-NC 4.0), `AudioCraft` weights (CC BY-NC 4.0):** Non-commercial restrictions block public commercial distribution.
* **Behzad Haki's `GrooveTransformer` / `MonotonicGrooveTransformer` repos (No LICENSE file):** All rights reserved by default; study the AIMC 2022 paper only.
* **ENST-Drums, IDMT-SMT-Drums, MDB Drums (CC BY-NC-*):** Internal R&D benchmarks only.
* **Slakh2100:** Lakh MIDI + commercial Kontakt sample provenance makes commercial distribution risky; GMD/E-GMD are superior for groove anyway.
* **Magenta Python package:** Legacy TensorFlow maintenance burden; re-implement ideas cleanly in PyTorch.
* **Ableton `.agr` files & copyrighted classic breaks:** Proprietary / copyrighted; never bundle or train released weights on them.

---

## Open Questions & Unverified Items

1. **Exact Elektron Octatrack binary project/pattern format specification & MIT-licensed libraries:** While `ot-tools` / `ot-tools-io` (GPL-3.0) documents the format in Rust and Octatrack's `-23/384` to `+23/384` bar microtiming range is confirmed, whether a **permissively licensed (MIT/Apache/BSD)** Octatrack project/bank writer exists is currently **`UNVERIFIED`** (should be audited separately before building the exporter).
2. **Empirical microtiming data for rominimal, dub techno, and UK techno / UK bass:** Peer-reviewed MIR measurements of microtiming/swing exist for jazz (Friberg & Sundström 2002; Dittmar et al. 2015, 2018), funk/hip-hop breakbeats (Frane 2017), and general EDM/house/techno production aesthetics (Butler 2006; Brøvig-Hanssen et al. 2020, 2022), but quantitative microtiming datasets or papers specifically measuring **rominimal** or **UK bass-adjacent techno** were **not found / `UNVERIFIED`**. Ghostbreak will likely need to rely on user-owned local reference analysis + preference learning to capture those sub-genre nuances.
3. **Pretrained checkpoint provenance for MIT-code MIR tools (`beat_this`, `Omnizart`, `VampNet`, `Demucs`):** Several repositories license their **code** under MIT while training **pretrained weights** on mixed or copyrighted academic music corpora (explicitly flagged by `beat_this` and `Demucs`). Exact commercial clearance of each checkpoint is **`UNVERIFIED`** and should be checked individually before any commercial distribution—or bypassed by training on CC BY 4.0 E-GMD/StemGMD + CC0 audio.
4. **Unverified secondary URLs / metadata explicitly flagged in text:**
   * Zenodo mirror record ID for ENST-Drums (`7443831` returned HTTP 404; official Télécom Paris URL `https://perso.telecom-paristech.fr/grichard/ENST-drums/` is verified).
   * Exact per-subdirectory licence/redistribution clearance inside `GiantSteps/drum-pattern-datasets` (no top-level GitHub `LICENSE` file) and custom `LICENSE` terms in `SonyCSLParis/DrumGAN`.
   * Exact download URL, size, and licence of Vogl et al.'s `TMIDT` dataset.
   * Exact ISBN of Paul Harkins (2019), *Digital Sampling: The Design and Use of Music Technologies* (Routledge), official archive URL for Nate Harrison (2004), and UiO DUO handle for Zeiner-Henriksen (2010).
   * Original 7" record labels for *Impeach the President* (Alaga), *Synthetic Substitution* (Sunburst), *It's a New Day* (GSF), and exact session drummer credit for Bobby Byrd's *Hot Pants (Bonus Beats)* cut vs. James Brown's 1971 *Hot Pants* single.
5. **Pitch-change extraction from polyphonic drum breaks:** Most ADT tools focus on *onset timing* and (rarely) *velocity*. Extracting continuous or per-hit **pitch/tuning shifts** (e.g., pitched snare rolls or sped-up jungle break pitch trajectories) from polyphonic drum audio is an open MIR problem; combining drum stem separation (e.g., LarsNet-style 5-stem separation) with per-hit spectral/fundamental estimation (`librosa.yin` / spectral centroid tracking) is a promising prototype direction worth testing early.
