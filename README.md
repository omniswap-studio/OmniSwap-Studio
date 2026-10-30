<p align="center">
  <picture>
    <img width="800" height="200" alt="OmniSwap-Studio_banner" src="https://github.com/user-attachments/assets/0439df58-2d24-4fce-9283-ecbfede17008" />
  </picture>
</p>

<h1 align="center"><b>An end-to-end rebuild of VisoMaster.</b></h1>

<p align="center">(Pre-Release Notes.)</p>

> [!NOTE]
> OmniSwap Studio is not currently available to the public. **However**, after a year in development, it will be releasing very soon. Everything described below is subject to change.

## Background

OmniSwap Studio is a full rewrite of VisoMaster using with the most capable, optimized technical foundations and standards available. It **completely replaces ~90%+ of the original VisoMaster code**, overhauling all core features and capabilities to achieve drastic improvements end-to-end.

## What OmniSwap Does Differently

1. **Separated the entire processing pipeline from the UI.** Everything that actually swaps faces now exists in a dedicated engine with **zero Qt imports**. It doesn't know a UI window even exists. It can handle a whole job from start to finish at the command line (images, videos + segments, HDR, VR180, denoiser, any number of jobs), and its output is **bit-identical** to what the original VisoMaster window records. Every setting, job, face/source, and the new Auto Detect system are all engine commands, not just button presses.
2. **Moved that engine into its own process.** The window process doesn't load a single model or touch CUDA at all, so heavy work can never freeze it.
3. **A typed, language-neutral API.** OmniSwap's new UI built from scratch, any external scripts, or an AI agent can drive it in whatever language, with nothing to break when the UI changes.

## Speed and Stability

| | VisoMaster | OmniSwap Studio |
|---|---|---|
| Worst UI freeze | ~38 s | ~0.17 s |
| Cached TensorRT model load | ~40 s each | 0.1–3.3 s |
| Frame processing (720p test job) | ~13.6 fps | 15-22 fps<b>*</b> |

<sup>*This result will become more definitive before release</sup>

- **Fixed the biggest bottleneck.** The GPU used to sit idle almost half the time waiting on Python to hand it work. Now it stays busy at all times, giving a verifiable 30%-60% speed increase for all frame processing compared to both VisoMaster and VisoMaster-Fusion.
- **UI freezing effectively eliminated.** No more modal "Loading…" popups, and heavy work like building embeddings runs in the background.
- **Cached models load directly.** OmniSwap always loads TensorRT models straight from the cached engine.
- **Crash recovery.** If the engine ever crashes (0 crashes in all of OmniSwap's testing), the UI window stays open, tells you, restarts it, and carries on.
- **No unswapped flashes.** A frame that fails never flashes the original, unswapped face into your recording.

## Verification

Every change to the swap pipeline is checked against real recordings, frame by frame, and strictly bit for bit. That's 52 checks covering 26 setting variations plus HDR, VR180, video + segments, images, and the denoiser, in addition to OmniSwap's 1,000+ automated tests, all of which pass.

## New Features

- **Auto Detect:** one button scans an entire video, finds every face, and groups them into target subjects via intelligent temporal analysis, so a blurry background face or a one-frame misdetection doesn't become its own person.
- **Pre-Detect:** runs Auto Detect, Known Targets and Auto Assign on any number of videos, producing a ready-to-process job per video. *Point it at a folder of videos, walk away, and let OmniSwap handle the entire standard swapping workflow for you.*
- **Merge review:** when the same person gets split in two (different angle, lighting, haircut), Auto Detect suggests merges with a confidence percentage.
- **Auto Assign:** with one click you can automatically assign all detected subjects by comparing their faces against your saved embeddings, then ranking them via face similarity and weighted demographics.
- **SubjectDB:** the memory behind all of it. Every person gets an identity made of several appearance clusters (head turned vs. straight on, etc.), so faces are recognized as who they are, not just "the box that was here last frame." An optional strict mode makes Play and Record swap only faces already tied to a known person, so a random passerby never gets swapped by accident.
- **Known Targets:** when you assign a detected target subject to a source embedding, OmniSwap can remember that, so they're automatically recognized and assigned in any future video.
- **Demographics:** every detected face and every saved source gets gender, race, age range and hair color labels.
- **Deep-Learning:** optionally, new people who appear while a video is playing get their own face card automatically, and OmniSwap learns what each subject looks like at various angles, in certain lighting, at different distances, etc. The more frames processed with Deep-Learning enabled, the better OmniSwap recognizes them.
- **Strict demographic matching:** Auto Assign can allow only sources that match on gender and race, with age and hair color either required or just preferred.
- **Known Targets management:** review, clear, export and import every remembered assignment, and see which ones point to missing source files.
- **Identity overlays:** when paused, overlays show who each face is, the match confidence, and which saved faces it looks like.
- **Auto Detect analytics:** live progress, how many faces passed quality, and a score chart with a suggested threshold you can apply in one click.

## A New App, Built From Scratch

OmniSwap Studio replaces VisoMaster's Qt window with a completely new interface. It is one app that runs two ways: as a **desktop app** and as a **web app** in any browser, on a laptop, tablet or phone. Both drive the same engine, so you can use either one or both at once on the same job. An edit made in one shows up on the next frame in the other.

- **Plain-language controls.** More than 300 raw settings are folded into a short set of controls whose effect you can see: Face model, Likeness, Detail, Skin texture, Color match, Edges, Face obstructions, Keep original, Expression and more <i>(these will likely change before release)</i>. Every raw setting is still reachable.
- **See exactly what changed.** Result, Before, Split and Mask views. Hold B to see the original footage, or hold a control's eye button to see the picture without that one control.
- **A real timeline.** Thumbnails along the ruler, change points to vary a person's look over time, and a Problems track that marks frames likely to render badly.
- **Cast and library.** The people in each shot sit in a cast bar. **Find everyone** runs Auto Detect, groups each person and casts faces you used before. New faces are built straight from a folder of photos.
- **Render queue.** Queue several shots and render them one after another.
- **Use it from anywhere.** The web app can run the engine on its own, with no desktop window. Other devices on your network join with a six-digit pairing code, and a browser can follow the desktop app's playhead or look at its own frame.
- **Keyboard first, both themes.** Shortcuts for playback, people, change points and render ranges, a Ctrl+K search for any setting or action, and dedicated dark and light themes.
- **Never blocked.** No modal "Loading…" popups. The engine reports its progress and the app stays usable.

## Other Changes

- **Fixes:** recordings no longer lose their final frames, a failed frame no longer stalls playback or recording, and several settings that were silently ignored or loaded from the wrong place now work as intended.
- **New models:** YOLO-Face 11x and 12m detectors, FairFace demographics, a CLIP hair color classifier and the XSeg DFL HQ occlusion model.
- **HDR10 end to end:** 16-bit HDR decoding, x265 HDR metadata and NVENC quality tiers.
- **Modern stack for RTX 50-series GPUs:** Python 3.12, CUDA 13, PyTorch 2.14, ONNX Runtime 1.31 and TensorRT 10.16, with an environment validator and a GPU backend benchmark.
- **Lower memory:** a cap on frames in flight keeps 4K HDR recordings within memory, and idle memory with a 4K job loaded dropped from 6.2 GB to under 5 GB.
- **Cleaner code:** one logging system, no silently swallowed errors, dead code removed, one shared inference helper instead of 63 hand-written blocks, and a quality ratchet that keeps it that way.

**Not yet implemented, TBD:** webcam input, virtual camera output, and audio during live playback. The initial release focuses on on the core swap pipeline, so these additional features will be reintegrated more optimally at a later point.
