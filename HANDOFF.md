# klobvision
## Development handoff

**Prepared:** November 19, 2026  
**Purpose:** A self-contained brief for a coding agent or developer starting the project.  
**Status:** Product and engineering design. No application has been implemented or benchmarked as part of this handoff.

**Execution boundary:** The current request authorizes design and planning only. The implementation milestones and coding-agent instructions below describe future work; begin application development only after the user explicitly starts that phase.

## 1. Read this first

Build **klobvision**, an open-source, GPU-accelerated visual show designer for live performance and experimentation.

The primary product is a **PC webapp**. It should render directly through browser GPU APIs, provide a separate control surface and clean output screen, and be enjoyable to play with before it becomes a large authoring system.

These decisions incorporate the user's latest clarifications and take precedence over earlier suggestions:

- **Webapp first, ideally running on a PC.** Android remains a secondary target.
- **Highest sustainable presentation frame rate.** There is no default application-imposed 60 fps cap. Aim to keep pace with the display/browser's available refresh cadence.
- **Fresh architecture and user experience.** prism is a reference for useful features. It is not a required fork, codebase, UI, architecture, or compatibility target.
- **Primary activity: live performance and experimentation.** Precise song timelines and unattended automatic shows can grow from this foundation.
- **Inputs:** system audio, particularly Spotify; DJ software such as Traktor; microphone/routed input; and audio files played by klobvision.
- **Music:** optimize initially for 4/4. Manual help with beats, bars, and phrase alignment is expected.
- **Song detection includes all four goals:** detecting track changes, identifying title/artist, matching live input to imported tracks, and locating playback position within a matched track.
- **No MIDI/controller integration required initially.**
- **2D visuals first.** Conventional 3D solids and hypercube projections have flexible priority.
- **Open source:** choose AGPL-3.0-or-later for original project code, preserving the separate licenses and notices of dependencies and assets.

Develop from these requirements. Any proposed reuse from prism should be a small, explicit engineering choice supported by suitability, licensing, and tests.

Apply the owner's existing global product and visual-design standards, restated in section 2 and the contribution rules in section 19. These come from the reviewed `xklob/dotfiles` base `AGENTS.md`. Keep this handoff self-contained for contributors; access to that source repository is not a prerequisite. Explicit project decisions and the user's current instructions take precedence over general defaults. [G1]

## 2. Product identity and first experience

klobvision combines editable geometric patterns, musical timing, and shader effects. A useful first session should feel like:

1. Open the app and immediately see an animated pattern.
2. Open a clean output window and move it to the projector or second monitor.
3. Choose system audio or load a track.
4. Set or detect the tempo.
5. Change shape sequences, spacing, color, and effects while the show keeps running.
6. Save an interesting look and trigger another on a beat or bar.
7. Record the visual output when desired.

The product should work locally without an account. Routine visuals, manual timing, file playback, and local analysis should remain usable offline after the application and required assets are available.

### Editor visual system // global product standards

Use the owner's **klob.io** visual language for the control interface, settings, onboarding, status, and install branding. The requirements below apply to the PC webapp and responsive Android interface; a later native companion should use appropriate native components with the same visual language. [G1]

| Area | Required behavior |
|---|---|
| Layout | Compact panels, clear controls and status, and a content-dominant layout. Keep values readable and controls usable as panels collapse or the viewport narrows. |
| Typography | Self-host **Space Grotesk** with the webapp and bundle it locally in any native app. Include its font license with the distributed assets. Routine use must not depend on an external font service. |
| Neutral surfaces | Light mode uses white or near-white neutral surfaces with dark text. Dark mode uses black or near-black neutral surfaces with light text. |
| Green accents | Use bright Paradigm green **`#00FF00`** sparingly for primary actions, selected states, and accents. Use darker **`#007500`** for green text or icons that need readable contrast on light surfaces. Use readable dark labels on bright-green filled controls. Verify text and control contrast in both themes. Avoid olive, muddy yellow-green, or lime substitutes and broadly green-tinted interfaces. |
| Theme selector | Provide **System / Light / Dark** controls, using icons by default with accessible names and a clear selected state. Start in System mode: use the detected OS/browser preference, falling back to dark when it is unavailable. Follow live system changes while System is selected. Persist explicit choices across reloads and restarts. |
| App naming | Keep **klobvision** lowercase throughout user-facing branding, including page titles, headers, PWA/launcher labels, catalog entries, release titles, and documentation. Preserve existing API identifiers, package IDs, repository paths, class names, and user-entered names. |
| Actions and copy | Primary action buttons use uppercase labels, such as `OPEN OUTPUT` and `SAVE LOOK`. App names stay lowercase even within those labels, such as `INSTALL klobvision`. Other labels and instructions are concise, functional, and easy to scan. Avoid slogans and unnecessary marketing prose. |
| Separators | Prefer restrained `//` separators in branding, headings, and compact label/detail pairs. Preserve ordinary punctuation where needed for prose, accessible labels, URLs, code, and data. |

**Editor appearance and creative content are independent.** Scene palettes, backgrounds, rainbow outlines, shaders, preview pixels, projected output, and recordings are authored content. Switching the editor theme must leave that content and its timing unchanged. Store theme mode as a device preference, separate from projects and looks. The creative presets below retain their own colors and black backgrounds.

Keep essential controls keyboard-accessible, make focus visible, and expose icon names and selected states to assistive technology. Compact panels must preserve access to audio/timing status, output blackout, and recording state.

### Visual reference: “Platonic Solids”

The prior conversation establishes the following creative direction:

- Black backgrounds with crisp, thin rainbow outlines.
- Square or staggered repeating layouts.
- Shape centers remain on the same grid while shapes morph.
- Shape size and spacing are independently adjustable.
- Continuous, smooth hue movement.
- Fast, snappy shape changes with time to hold each resulting shape.
- A sequence that goes **triangle → square → pentagon → hexagon → triangle**, wrapping directly.
- An option for touching shapes to share the same hue at their contact point.
- Later extensions to circles, square spirals, Platonic solids, and projected hypercubes.

These are starting presets and visual primitives, not restrictions on every future scene.

### First demo preset

Create a “Polygon Pulse” preset at **124 BPM**:

- One shape change per quarter-note beat.
- Four changes complete the repeating four-shape cycle.
- Approximately 0.484 seconds per beat and 1.935 seconds per four-beat cycle.
- Start with a morph duration of one eighth of a beat, approximately 60 milliseconds.
- Expose shape-advance interval and morph duration separately. Hold duration is the remaining interval; editing hold duration updates the interval. Clamp morph duration to the interval unless a deliberate overlapping-transition mode is added.
- Hue continues moving smoothly while geometry changes quickly.
- Optional row, column, checkerboard, or radial phase offsets create traveling patterns.

Treat these timings as a useful starting design. They are not hardcoded global behavior.

## 3. Feature reference: prism

Reference inspected: [xklob/prism](https://github.com/xklob/prism), commit **97c0acf8e9c794d6925ed279bc660b3153f1f4ba**, version **1.15.0**.

This was a source/documentation review. No prism builds, tests, or physical-device validation were performed during this handoff.

Use the following as a feature checklist, with a new implementation and UI where appropriate:

| Reference feature | Direction for klobvision |
|---|---|
| Procedural kaleidoscopic, tunnel, and fractal scenes | Add selected generators alongside the new morphing-grid system |
| Per-pattern geometry, motion, color, and audio controls | Use consistent parameter definitions and clear scene-specific controls |
| Saved looks, favorites, randomization, and crossfades | Support portable presets, duplication, favorites, undo, and bounded randomization |
| Beat/bar/phrase guidance and manual corrections | Provide one musical clock with visible confidence and clear manual authority |
| Waveform, file playback, and track analysis | Support both preparation and playback while analysis proceeds |
| Bloom, blur, clean recording, and adaptive quality | Build browser-native equivalents and a more general effect chain |
| Song metadata, optional recognition, and diagnostics | Use independent adapters and show the source and reliability of each result |
| Crowd/shared timing | Consider paired phone controls and multiple receivers after local two-screen operation works |

prism's current code contains four scene IDs: Kaleido, Wormhole, Mandelbrot, and Kaleidoscope. Some older README paragraphs still describe removed generators. Its current browser dashboard controls native clients; it is not a standalone web visualizer. Its song assistance also does not provide a local acoustic fingerprint index or musical-key estimator. See [P1–P6] below.

Do not inherit incidental limits such as a fixed 32-look library, one-file cache, complete-analysis-before-playback, specific native recording formats, or a native Android dependency.

## 4. Recommended technology choices

| Area | Initial choice | Reason |
|---|---|---|
| Language/build | TypeScript, Vite | Straightforward browser development and static deployment |
| Editor UI | React | Forms, compact panels, scene management, and responsive layout following the section 2 visual system |
| Visual runtime | Three.js with WebGL2 | GPU geometry, instancing, custom shaders, render targets, and a path to later 3D |
| Effects | An explicit render-pass chain, using Three.js facilities where useful | Ordered effects, predictable compositing, reusable GPU resources |
| Audio I/O | Web Audio API | Local playback, capture streams, and a common audio graph |
| Sample acquisition | AudioWorklet | Sample-clock timestamps and lightweight collection outside UI work |
| Heavy analysis | Dedicated Workers; WASM where useful | Keep inference, file analysis, and DSP off the UI/rendering thread |
| Persistence | IndexedDB; OPFS where useful and supported | Projects, analysis caches, and optional local assets |
| Distribution | Static HTTPS webapp with optional PWA installation | Usable without a desktop wrapper |
| Remote features | Optional small, self-hostable session service | Pairing and remote control without a mandatory account |

Three.js's current WebGLRenderer uses WebGL2, and EffectComposer supports ordered post-processing passes. These are suitable building blocks; the application still owns its scene model and timing. [S1, S2]

**WebGL2 is already GPU acceleration.** WebGPU is a future renderer or compute option when a benchmark or required feature warrants it. Keep visual generators behind a modest renderer interface, but build one working backend first.

React must not own per-frame scene state. The visual runtime reads compact mutable runtime state, updates GPU uniforms/buffers, and renders independently.

### Suggested code boundaries

Begin with a simple repository organized around these responsibilities. Separate packages only when useful.

| Module | Responsibility |
|---|---|
| editor | Control UI, scene browser, inspector, mappings, project commands |
| core | Project schema, parameters, musical clock, cues, deterministic modulation |
| renderer | GPU resources, generators, layers, compositing, effect passes |
| audio | Sources, worklet, DSP, analysis workers, analysis-provider interface |
| tracks | File playback, cached analysis, annotations, identity and matching |
| output | Output-window lifecycle, session messaging, preview, recording |
| adapters | Optional recognition services, future DJ integration, Android input, remote control |

Keep pure clock, mapping, and project logic independent of React, Three.js, and any one audio-analysis library.

## 5. Rendering and geometry

### GPU responsibilities

Use the GPU for drawing, repeated geometry, morph interpolation, composition, and shader effects. Upload static geometry once where possible. Reuse render targets and buffers.

For grids, prefer instancing or an equivalently efficient procedural technique. Avoid one DOM/SVG node per visual element and avoid rebuilding every contour on the CPU for every frame.

A sensible first shape implementation is:

- Represent each simple closed shape using corresponding contour samples.
- Normalize winding, starting point, orientation, and scale.
- Keep sample count stable across a morph.
- Interpolate corresponding positions in the vertex shader.
- Instance the result across the layout, with per-instance position, scale, rotation, and phase.
- Preserve sharp vertices and visually inspect intermediate contours.

Use instanced triangle/ribbon geometry or antialiased shader strokes for adjustable outline thickness. Do not depend on native WebGL line width: Three.js documents that LineBasicMaterial.linewidth is ignored by WebGL. [S18]

GPU distance-field shapes are another option for particular generators. Evaluate the actual deformation: field interpolation and contour interpolation have different appearances. The first acceptance criterion is a real, attractive shape deformation through the requested sequence.

SVG can become an import format for supported simple silhouettes. Compound paths, holes, text, masks, and arbitrary topology are separate scope. Do not make arbitrary SVG morphing a prerequisite for the built-in shapes.

### Independent controls

Expose shape sequence, size, grid spacing, layout, orientation, stroke width, fill/outline mode, morph timing, hold timing, sequence rate, and phase offset pattern separately.

Support screen aspect ratio without distorting the shapes. Define line width and layout units consistently across output resolution and dynamic render scale.

### Color behavior

Offer both per-shape color and a shared spatial color field.

In spatial mode, sample color using a shared scene coordinate and time. Touching contours can then agree in hue before post-processing. Changing grid density should not arbitrarily reset color phase. Allow the hue clock to run independently of the musical clock.

Define color space, blending, bloom inputs, and final output conversion explicitly so effects do not accidentally apply gamma conversion twice.

### Effects and layering

Start with a compact set:

- Bloom/glow and blur.
- Color adjustment and hue movement.
- Mirror/kaleidoscope.
- Displacement/distortion.
- RGB separation.
- Trails or feedback.

An effect can be enabled, bypassed, reordered, and mapped to controls. Stateful feedback requires dedicated history textures and explicit reset/resize behavior.

Express feedback/trail decay, smoothing, and exposure accumulation in elapsed seconds rather than per-frame constants. A preset should retain comparable motion and persistence at different display refresh rates.

A scene should support one generator initially, then a small stack of layers with opacity, transforms, blend mode, and optional masks. Avoid building a generic node editor before the direct workflow is enjoyable.

Preserve three distinct concepts: geometric morphs, parameter interpolation, and full-scene transitions.

## 6. Musical controls and modulation

### One musical clock

The clock represents tempo and phase continuously. It accepts information from manual input, live estimates, file analysis, or a future external synchronization adapter.

Minimum state:

- Source identity and transport epoch.
- Timestamp in a documented clock domain.
- BPM, continuous beat position, and meter.
- Bar origin and phrase origin, when established.
- Phrase length, initially 8/16/32 bars.
- Confidence/evidence score and provenance for beat, bar, and phrase separately.
- Running, paused, acquiring, manual, guided, holding, or stale status.
- Adjustable synchronization offset.

Keep manual settings authoritative until the user releases them. Tempo lock, beat alignment, and phrase alignment should be independently understandable.

Provide tap tempo, BPM entry, half/double tempo, phase nudge, “beat starts here,” “bar starts here,” “phrase starts here,” and return-to-auto controls.

Returning to automatic tracking should begin from the user's corrected timing and let evidence refine it. A song's catalog BPM is a prior, not evidence of the live downbeat position.

### Modulation system

A mapping contains source, target, range, response curve, smoothing/attack/release, amount, and optional quantization.

Initial sources:

- Beat and bar phase.
- Beat/bar impulses.
- Phrase position or boundary when valid.
- RMS/energy and bass/mid/treble bands.
- Onset strength.
- Slow oscillators and seeded random modulation.
- Manual macros.
- Stable key information, where available.

Examples: bass → scale; beat → shape advance; four bars → rotation change; phrase → scene cue; treble → fine distortion.

Keep the saved base value separate from the modulated runtime value. Undo, saving, and randomization should not accidentally commit transient audio samples into presets.

Offer a small set of assignable on-screen macros. MIDI is not part of the initial release.

### Automatic mode

Automatic mode selects from approved scenes and changes parameters within user-defined bounds. Provide intensity, change frequency, effect permissions, and parameter locks. Schedule large changes on valid musical boundaries.

Automation is secondary to direct performance. It should be possible to take over immediately.

## 7. Audio inputs and platform capabilities

The initial supported environment should be current desktop Chrome/Edge on Windows, tested against real user playback routes. Other browsers should retain file playback, manual visuals, and supported input modes.

| Source/platform | Planned behavior |
|---|---|
| Local file, PC browser | Play and analyze inside klobvision |
| Microphone or routed input device | Use getUserMedia with appropriate input selection |
| System/tab audio, Windows Chromium | Request user-selected capture and verify that an audio track actually arrives |
| Native Spotify/Traktor on PC | Validate the actual OS output route; offer a routed input when ordinary browser capture does not receive it |
| Android browser/PWA | Responsive visuals/control, file playback, microphone where supported |
| Other-app audio on Android | Optional native capture companion later; source-app policy still applies |

getDisplayMedia requires a video track and returns audio only when supported and selected. Requesting audio is not proof that sound is available. Capture UI should distinguish permission denial, absent audio track, silence, quiet audio, and a stopped stream. The visualizer should consume the audio and avoid using captured desktop images as visual content. Preserve required track lifetimes and release all tracks on disconnect. [S3]

Current browser compatibility data does not provide ordinary getDisplayMedia system capture in Android Chrome. Native Android playback capture is conditional on OS permission and the producing app's capture policy. A PWA installation does not remove those restrictions. [S4, S5]

Spotify is an example source, not a mandatory service integration. The default PC app should work through generic local audio capture and files. Do not require Spotify credentials or a provider SDK for core operation.

For Traktor, verify master-output routing on the actual machine and audio interface. Do not equate master audio, headphone cue audio, and every device/driver output. A later native helper is an optional capability if a required route is inaccessible to browser APIs.

Prevent captured system audio from being played back into itself. Analysis and monitoring are separate controls.

## 8. Analysis pipeline and backend selection

Use this conceptual path:

**Source → sample collection → worker analysis → timestamped musical state/features → modulation and visual runtime.**

The worklet should do bounded lightweight work. Heavy feature extraction, inference, key estimation, and file analysis belong in workers. Keep queues bounded and publish current results without accumulating stale visual updates.

### Musical capabilities

- **Live:** energy/spectrum, onset strength, tempo, beat phase, bar hypotheses, phrase assistance, and slower key estimation.
- **Files:** waveform, tempo/beat maps, bar/phrase candidates, global or regional key estimates, and editable annotations.
- **Manual:** fully functional independent clock and corrections.

Pitch, chroma, and musical key are distinct outputs. Allow key to be unknown during uninformative or mixed passages. Apply hysteresis so a noisy estimate does not constantly switch palettes.

### Candidates to evaluate

| Candidate | Useful role | Selection note |
|---|---|---|
| Meyda | Basic audio features | A feature library, not a complete rhythm/structure engine |
| Essentia.js | Tonal analysis and selected file/live algorithms | Evaluate particular algorithms; whole-track methods are not automatically causal |
| BeatNet | Learned beat/downbeat activations | Evaluate model/runtime/frontend together; prism is one example integration |
| Beat This! Live | Online beat/downbeat tracking | Promising alternative; integration and sustained performance need assessment |
| ONNX Runtime Web | Browser inference for compatible exported models | WASM worker baseline; GPU inference only after measurement |

Meyda supports live and offline extraction. Essentia.js exposes analysis through WASM, while Essentia explicitly states that RhythmExtractor2013 requires whole-track statistics. Beat This! Live publishes MIT-licensed code and weights. [S6–S9]

Do a short, comparative technical spike, then choose and document a backend. Do not build a product dependency on a research model before checking runtime support, distribution rights, latency, and behavior on representative music.

Keep the analyzer replaceable. Exact parity with prism is not a requirement.

Select live rhythm tracking, offline file analysis, and key estimation independently. They share versioned result types and timestamp conventions, but need not use the same library or model. Each provider must declare whether it is causal, its required sample rate, and the latency or lookahead associated with its output timestamps.

If an existing model is reused, reproduce its expected resampling, frontend, and recurrent-state handling. Process sequential recurrent frames in order; keep separate states for live input and background file jobs. Use actual browser sample rate, not a hardcoded assumption.

ONNX Runtime Web offers a WASM path and GPU providers with differing operator coverage. Begin with a CPU/WASM worker where practical to preserve GPU budget for rendering. Its runtime/WASM versions must match, and multithreading has cross-origin-isolation requirements. Keep a supported single-thread baseline. [S10, S11]

## 9. Timing and continuity rules

Timestamp audio at its sample origin. Analysis-message arrival time is not the time of the beat.

Map audio time, worker results, output-window time, and file position explicitly. For internal playback, getOutputTimestamp can help map the audio context to the performance clock. External audio still needs an adjustable end-to-end offset. [S12]

Important invariants:

1. One timing authority per active session.
2. One transition/cue event per intended boundary.
3. A seek, source switch, restart, or meaningful transport discontinuity increments an epoch.
4. Realtime results from old transport epochs cannot restore old session state or trigger cues. Background file jobs have separate identities as described below.
5. Clock correction and newly acquired confidence do not count as musical boundary crossings.
6. Analysis revisions affect future scheduling; they do not replay past cues.
7. An uncertain bar origin must not be presented as a detected first beat.
8. Silence, disconnected input, and weak musical evidence have distinct states.
9. Brief confidence loss can coast on an established clock; prolonged uncertainty suppresses inferred structural cues.
10. Manual and free-running modes remain playable when automatic analysis is unavailable.

Use time-based interpolation and phase sampling. Never advance the musical show by counting render frames.

Phrase support starts with 4/4, a configurable bar count, manual origin, and optional structure-change evidence. Explicitly label counted/manual phrases and inferred structure. “Drop,” “chorus,” and “breakdown” labels are a future analysis extension.

## 10. Local files and persistent analysis

Offer two workflows:

- **Prepare:** complete analysis before performance, inspect the waveform, and correct annotations.
- **Play now:** begin playback once it is ready; analyze concurrently and improve future timing information as results arrive.

Track playback readiness separately from analysis completion. Show analyzed ranges, pending regions, and estimated versus user-corrected markers.

The first implementation can decode manageable complete files and analyze samples progressively. decodeAudioData requires complete file data; it is not a streaming compressed-audio decoder. Larger files may need a separate demux/decode solution and explicit memory budgets. [S13]

Use an audio element or another supported playback path for files that should begin before full analysis preparation. Validate that playback and analysis use aligned timestamps and consistent resampling. Prioritize active performance over background file jobs.

Persist analysis using file content identity plus analyzer/model version and relevant settings. Preserve user corrections separately so reanalysis does not erase them.

Track background file jobs by content identity, analysis configuration, and job generation separately from the session's transport epoch. After a seek, valid results may still populate the file cache; applying them to active playback must check the current source, position, and epoch. Canceled or superseded jobs must not overwrite newer analysis.

Support play/pause, seek, restart, waveform navigation, manual beat/bar/phrase correction, and basic loop points as the file workflow develops. Seeking must invalidate stale live inference and pending cues while immediately reusing valid cached analysis.

Keep projects portable without automatically embedding large music files. Store asset references and hashes, with relinking on another machine. Including audio in an export should be an explicit choice.

## 11. Song detection and synchronization

All four requested behaviors remain in scope, with separate adapters and milestones.

| Behavior | Proposed implementation |
|---|---|
| A new track starts | Reliable transport/metadata events where available, plus an explicitly uncertain acoustic change detector |
| Identify title/artist | Own file tags, optional platform/provider metadata, local-library identity, or optional acoustic recognition service |
| Match live audio to imported tracks | Local fingerprint index with candidate scores and rejection of ambiguous matches |
| Locate playback position | Own player transport; local-match offset and playback-rate estimate; provider position as supporting information |

A browser cannot generically read another application's private player metadata. Source capture provides sound, not necessarily title and artist.

Treat file identity and acoustic alignment as separate results. A correct title does not establish live beat phase or sample-accurate song position.

### Local matching milestone

Index user-imported music and evaluate existing fingerprinting approaches. Test clean playback, noise, repeated sections, edits/remixes, moderate speed changes, seeks, and crossfades. Require a stable match over multiple observations before applying track-specific cues.

Chromaprint targets near-identical audio and explicitly does not claim general-purpose fingerprint robustness. Do not assume it alone solves a live DJ mix. [S14]

Return match confidence, offset, observed playback rate, timestamp, and validity interval. Drop or downgrade an alignment after contradictory evidence. During two-track overlaps, retain uncertainty or multiple candidates; do not force a false single-track answer.

### Optional catalog recognition

prism illustrates a useful pattern: local information first, optional catalog lookup, and opt-in acoustic recognition. klobvision can use a similar hierarchy with independently chosen providers.

Network recognition must be optional, bounded, and asynchronous. Show when an audio excerpt will leave the device. Never make a provider response the master beat clock. Never embed private API keys in a public frontend bundle.

External keys may require a user-controlled backend or provider-supported browser authorization. Treat pricing, CORS, provider terms, and browser key handling as integration checks, not assumed capabilities. Core visuals and analysis continue if a service fails.

## 12. Control screen and output screen

### Same-PC workflow first

- Control page: preview, scene/preset library, layer/effect inspector, mappings/macros, audio transport, timing status, and output controls.
- Output page: only the rendered composition.
- `OPEN OUTPUT` uses an explicit user gesture. The user can move the window to the desired monitor and enter fullscreen.
- If programmatic display placement is available, use it as an enhancement; manual placement remains supported.
- Blackout, freeze, resume, and output reconnection should be simple actions.

Apply the section 2 visual system to control-page chrome and any setup/reconnection UI. The clean output contains only authored visuals. Theme changes must not alter the output, reset scene state, disturb musical timing, or recreate the renderer. Keep essential status and performance controls discoverable in compact and expanded layouts.

Use same-origin messaging for scene commands and timestamped state. BroadcastChannel or a MessagePort can carry control/state data. Do not assume MediaStream objects can be sent through these channels.

Choose one session owner for audio/transport. The visible output window owns its rendering loop, so editor UI work does not directly schedule every output frame.

A separate window does not guarantee a separate browser process or event loop. Stress-test editor interaction while output runs at the desired cadence. Consider worker rendering with OffscreenCanvas if profiling reveals significant UI contention, and validate its recording and lifecycle behavior before adopting it.

Validate control-window minimization, output focus changes, mixed-refresh monitors, popup blocking, and output-window closure. Browser background scheduling varies; observe actual behavior instead of claiming a permanent background-rendering guarantee.

### Preview

Prioritize the projected output. The control preview may use lower resolution and refresh cadence.

For deterministic geometry, a separate lightweight preview can follow the same clock and seed. Stateful feedback can diverge between separately rendered histories. Provide a tested preview of the actual output stream where exact feedback preview matters. Avoid CPU pixel readback every frame.

### Cross-device operation

After local two-screen operation works, support an Android phone/tablet as a paired controller for the PC session.

Use authenticated pairing and a browser-compatible transport. A self-hostable service or WebRTC signaling path is sufficient. An HTTPS PWA cannot assume arbitrary plaintext local WebSocket access will work.

Send commands, clock state, and features. The PC renders using its GPU. Extend to additional receivers only after clock conversion, reconnection, and stale-state handling work.

## 13. Projects, presets, and commands

Separate three kinds of state:

| State | Examples |
|---|---|
| Project/preset | Scenes, layers, generators, effects, mappings, palettes, cues, assets |
| Session | Active source, transport, manual timing, current analysis, output connection |
| Device preferences | Preview quality, preferred devices, local storage, display preferences, theme mode (`system`, `light`, or `dark`) |

Recalling a look must not change the capture device or unexpectedly restart playback.

Persist the selected theme mode, including `system`, rather than replacing it with the currently resolved light/dark appearance. Project import and look recall must not overwrite device theme preferences. Follow OS changes only while System is selected; an explicit Light or Dark choice remains in force until changed.

Use versioned JSON with stable IDs. Provide project import/export, preset import/export, schema validation, migrations, and an undoable command model for edits.

Minimum concepts:

- Project: schema version, scenes, assets, performance configuration.
- Scene: layers, effect chain, mapping definitions, macros, seed.
- Generator: type/version, parameter values, asset references.
- Parameter: ID, units, range, default, mapping eligibility.
- Cue: target, quantization, transition, transport epoch.
- Audio feature frame: source time, epoch, energies/spectrum, analysis provenance.
- Musical state: tempo, phase, origins, confidence, manual state.
- Track analysis: content ID, analyzed ranges, engine version, estimates, user annotations.

Use typed values and stable units. Avoid exposing implementation-specific shader uniform names as the public project format.

## 14. Recording

Provide clean visual recording with optional audio from the active source. Editor controls and diagnostics must stay outside the captured canvas.

Start with canvas.captureStream and MediaRecorder where supported. Probe codec/container support and report what was actually configured. Do not promise native prism's H.264/AAC MP4 output on every browser. [S15, S16]

Establish a tested stream path between the output canvas and the session's audio source. Same-origin window access can be used deliberately; do not assume a broadcast channel transfers media streams.

Support video-only recording, playback/capture interruption, a clean stop, and downloadable output. Bound memory use and flush chunks appropriately.

Recording cadence and encoding capacity are separate from live rendering cadence. The recorder must not silently impose a 60 fps cap on the live output. Negotiate recording quality independently and show a lower recording rate if the encoder cannot keep up.

Deterministic offline video export, automatic transcoding, and GIF export are later enhancements.

## 15. Performance policy: as high as the device can sustain

**Default: no application-imposed render-frame cap.**

Render on every available requestAnimationFrame callback and aim to sustain the display/browser's effective presentation cadence. Browsers generally synchronize callbacks with display refresh and commonly pause them for hidden pages. Faster animation must still use elapsed time rather than frame count. [S17]

Support high-refresh displays such as 120, 144, 165, 240 Hz, and higher when the hardware/browser can present at those rates. A physically 60 Hz display cannot show 240 distinct frames per second.

Do not waste CPU in a busy loop to advertise frames that are never presented. Distinguish render callback rate, GPU workload, recording cadence, and actual display behavior.

Performance controls:

- Output resolution and render scale.
- Automatic quality with a “favor frame rate” default.
- Optional user-selected cap or battery-saving mode.
- Separate preview quality/cadence.
- Per-effect quality, bloom resolution, and bounded expensive shader work.
- Explicit handling of GPU context loss and resource recovery.

Prefer adjusting pixel cost and effect quality before reducing output cadence. Use hysteresis so adaptive quality does not visibly oscillate. Preserve the chosen composition and disclose meaningful quality changes in the operator UI.

### Metrics

Measure frame-time distributions, missed presentation budgets, shader compilation stalls, analysis backlog, and memory/resource trends. Use GPU timing queries where supported and valid; do not mistake JavaScript submission time for completed GPU work.

Examples of nominal frame budgets are approximately 6.94 ms at 144 Hz and 4.17 ms at 240 Hz. The actual available GPU budget is smaller because the compositor and other work also consume time.

Benchmark across:

- Basic polygon grid.
- Dense grid plus several effects.
- Layered scenes and crossfades.
- Feedback-heavy preset.
- Audio analysis active.
- Recording active.
- Control and output on displays with different refresh rates.
- Sustained sessions and Android's reduced-quality mode.

Use 1080p, 1440p, ultrawide, and higher resolutions as benchmark points, not a fixed resolution ceiling. Set success thresholds against the tested display cadence and scene profile. Record actual results; do not claim high-refresh or hardware-GPU validation from a software-rendered/headless run.

## 16. Android scope

The webapp should have a usable responsive Android layout for experimentation, local-file visuals, microphone input where supported, and remote control of a PC.

Android system-audio capture may later use a small native companion. That companion supplies timestamped input/features or integration services; the PC webapp remains independently functional.

prism's native capture behavior is a reference. Any specialized compatibility mechanism requires its own explicit decision and device validation. Android-native packaging, native recording, and external-display support should not delay the primary web release.

## 17. Implementation milestones

These milestones are an implementation sequence, not a reduction of the requested feature set.

### M0: Browser feasibility and project skeleton

- Initialize klobvision with the chosen license and basic project structure.
- Establish neutral surface/accent tokens, locally hosted Space Grotesk with its license, and lowercase app branding using section 2.
- Render a GPU scene with frame-time diagnostics and no fixed frame cap.
- Open the separate output window.
- Verify real system audio from the target Windows browser.
- Verify local-file playback and an analysis worker.
- Measure high-refresh behavior on available real hardware.

**Exit:** A browser session demonstrates the actual GPU/capture/output path. Unsupported routes are clearly identified.

### M1: Playable geometric instrument

- Polygon-grid generator and the 124 BPM reference preset.
- Independent size, spacing, hue, morph, and hold controls.
- Manual tempo/bar/phrase controls.
- Initial effect chain, bounded randomization, scene save/recall, undo.
- Output blackout/freeze and lightweight preview.
- Apply the editor visual system throughout the usable interface, including accessible icon controls and persistent System/Light/Dark selection.

**Exit:** A user can improvise a visually satisfying show with manual musical timing.

### M2: Live music and file intelligence

- Select and integrate an automatic beat/downbeat backend after comparison.
- Energy/band/onset features and mapping UI.
- 4/4 bar tracking and manually assisted phrase tracking.
- Live and file key estimation with uncertainty.
- Prepare/play-now file workflows, waveform, seek handling, persistent analysis.
- Manual corrections and analysis-provenance UI.

**Exit:** Automatic tracking helps performance and remains correctable; loaded files can play independently of full analysis completion.

### M3: Show-design depth and recording

- Layers, improved transitions, quantized cues, macros, and effect reorder/bypass.
- Selected procedural/fractal generators inspired by the feature reference.
- Clean browser recording with audio.
- Project import/export and optional PWA caching.
- Sustained high-refresh profiling and adaptive-quality refinement.

**Exit:** klobvision is useful for a real hobbyist performance and can save/share reproducible projects.

### M4: Complete song detection and secondary clients

- Track-change detection with provenance.
- Title/artist identification through chosen adapters.
- Local-library indexing and live match/position alignment.
- Optional online recognition and failure handling.
- Paired Android control; optional native input adapter as justified.

**Exit:** Each of the four song-detection meanings has an implemented, tested path with its practical limits documented.

### Later extensions

3D solids and hypercube projections, imported SVG morphs, precise song timelines, additional output transports, multiple receivers, custom shader authoring, and richer automatic show rules.

## 18. Verification that matters

Favor tests for timing, state, geometry, and capability boundaries over tests that merely mirror UI implementation.

| Area | Meaningful checks |
|---|---|
| Interface and branding | Neutral surfaces and specified accents in both themes; readable text/controls and visible focus; keyboard-operable theme icons with accessible names and selected states; uppercase primary actions with lowercase klobvision branding, including install labels |
| Theme and font behavior | System follows live OS changes and falls back to dark when undetectable; explicit choices survive restart; project/scene changes preserve device preferences; Space Grotesk loads from local assets and its license is distributed |
| Editor/output independence | Theme changes preserve the authored composition, scene state, and musical timing. Layout changes may resize the preview without resetting animation or altering projected/recorded composition. Essential controls remain accessible, and neither operation introduces sustained output-performance regressions. |
| Morphing | Correct endpoints, direct hexagon-to-triangle wrap, stable centers, predictable intermediate contour |
| Musical clock | Tempo changes, half/double correction, pause/seek, manual release, confidence loss, no duplicated cues |
| Analysis | Annotated real 4/4 tracks plus synthetic known-timing fixtures; acquisition and failure cases reported separately |
| Source capture | Real Spotify/Traktor routing on the target PC; permission denied, no audio track, quiet input, source ended |
| File workflow | Play during analysis, canceled jobs, seek into unanalysed areas, cache invalidation, edits surviving reanalysis |
| Song matching | Wrong candidates/remixes, repeated sections, unknown tracks, tempo changes, crossfades, stale provider replies |
| Output | Separate clean window, different refresh rates, control minimization, reconnection, context loss |
| Recording | Clean frames, supported format, audio alignment, interruption, live output cadence preserved |
| Persistence | Round-trip projects, stable IDs, migrations, absent assets, user annotations retained |
| Performance | Real-device frame times and sustained memory/resource behavior at several output sizes |

Confidence must describe evidence, not imply a statistically calibrated probability unless calibration was actually performed.

A beat detector looking convincing on one song is not validation. A synthetic timing fixture is useful for exact clock checks; real music is needed to assess musical robustness.

## 19. Instructions to the implementing agent

The following instructions apply once implementation is requested. During the current planning phase, refine this document and evaluate design choices without starting application development.

1. Treat sections 1, 2, and 15 as authoritative product constraints. Apply the relevant current global and repository `AGENTS.md` instructions when available; the standards restated here remain sufficient to begin without access to the owner's dotfiles.
2. Build a fresh klobvision application in this repository. Use prism to understand feature ideas.
3. Read current primary documentation and existing libraries before implementing specialized graphics, DSP, or recognition.
4. Keep the first vertical slice small: GPU grid, controls, separate output, manual clock, save/recall.
5. Make the PC browser standalone. Audio analysis, playback, and rendering must not require Android.
6. Compare analysis candidates in a bounded spike, record the choice, then implement it.
7. Keep expensive operations off the UI/rendering path and maintain source timestamps.
8. Preserve modular boundaries without constructing a large general-purpose plugin framework.
9. Make uncertainty visible and manual correction easy.
10. Validate the actual high-refresh and source-capture paths on real target hardware.
11. Track all four song-recognition requirements even when they land in later milestones.
12. Deliver working increments with concise setup instructions, known capability limits, and measured evidence.

### Repository and contribution rules

Carry forward the applicable global working rules alongside the product standards. [G1]

- Preserve unrelated work and keep changes scoped to the current request. The present authorization remains documentation and planning only.
- Keep shared instructions agent-agnostic. Label any tool-specific workflow explicitly.
- Keep product and contributor-facing prose plain and concise, with no em dashes. Use symbols only when they improve readability; fence multiline code.
- Before a public write, verify the active project account, repository permissions, destination, human author/committer identity, approved email, and signing configuration. Keep authentication scoped to the project and avoid silently inheriting unrelated machine defaults.
- For AI-assisted commits, credit the appropriate human project identity as author and committer, then the primary agent in a `Co-authored-by` trailer. Include the verified agent product, model family/version, and reasoning effort; mark unavailable fields `unknown`. Verify these against the active session rather than copying examples or configured defaults. Credit the primary agent rather than every subagent.
- Include only reusable project requirements in public instructions. Keep unrelated personal context, credentials, machine-specific paths, and private identity mappings out of repository content and public history.

### Licensing and reuse

Apply AGPL-3.0-or-later to original klobvision code. Keep dependency, shader, model, and asset provenance separate. Model licenses do not automatically match the code license.

The reviewed prism snapshot lacks a root project LICENSE file, although third-party model/runtime notices exist. If code is selectively reused, explicitly establish the license for the owned code and retain upstream attribution. Referencing behavior does not require copying implementation. [P6]

## 20. Sources and reference material

Primary sources were checked during preparation. Recheck compatibility and exact dependency versions when development begins.

### Owner product and working standards

- **[G1]** [Base `AGENTS.md` in xklob/dotfiles](https://github.com/xklob/dotfiles/blob/b7ec9a777ef3005df6e69669671572aa09f3ba47/AGENTS.md), reviewed November 19, 2026. Relevant visual, product, and contribution requirements are restated in this handoff. The source identifies [klob.io](https://klob.io) as the visual baseline; this update applies the source's explicit rules. Contributors do not need source-repository access to follow them.

### Web graphics and media

- **[S1]** [Three.js WebGLRenderer](https://threejs.org/docs/pages/WebGLRenderer)
- **[S2]** [Three.js EffectComposer](https://threejs.org/docs/pages/EffectComposer)
- **[S3]** [MDN getDisplayMedia](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getDisplayMedia)
- **[S4]** [MDN browser compatibility data for MediaDevices](https://github.com/mdn/browser-compat-data/blob/main/api/MediaDevices.json)
- **[S5]** [Android audio playback capture](https://developer.android.com/media/platform/av-capture)
- **[S12]** [MDN AudioContext.getOutputTimestamp](https://developer.mozilla.org/en-US/docs/Web/API/AudioContext/getOutputTimestamp)
- **[S13]** [MDN decodeAudioData](https://developer.mozilla.org/en-US/docs/Web/API/BaseAudioContext/decodeAudioData)
- **[S15]** [MDN canvas.captureStream](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement/captureStream)
- **[S16]** [MDN MediaRecorder.isTypeSupported](https://developer.mozilla.org/en-US/docs/Web/API/MediaRecorder/isTypeSupported_static)
- **[S17]** [MDN requestAnimationFrame](https://developer.mozilla.org/en-US/docs/Web/API/Window/requestAnimationFrame)
- **[S18]** [Three.js LineBasicMaterial and WebGL line-width limitation](https://threejs.org/docs/pages/LineBasicMaterial.html)

### Analysis candidates

- **[S6]** [Meyda](https://github.com/meyda/meyda)
- **[S7]** [Essentia.js](https://github.com/MTG/essentia.js) and [key extraction](https://essentia.upf.edu/reference/std_KeyExtractor.html)
- **[S8]** [Essentia beat-detection tutorial and real-time limitation](https://essentia.upf.edu/tutorial_rhythm_beatdetection.html)
- **[S9]** [Beat This! Live](https://github.com/CPJKU/beat_this_live) and [BeatNet](https://github.com/mjhydri/BeatNet)
- **[S10]** [ONNX Runtime Web](https://onnxruntime.ai/docs/tutorials/web/)
- **[S11]** [ONNX Web runtime options](https://onnxruntime.ai/docs/tutorials/web/env-flags-and-session-options.html) and [deployment](https://onnxruntime.ai/docs/tutorials/web/deploy.html)
- **[S14]** [Chromaprint scope](https://github.com/acoustid/chromaprint)
- [Essentia library/model licensing](https://essentia.upf.edu/licensing_information.html)

### prism feature-reference snapshot

All links below are pinned to the reviewed commit.

- **[P1]** [Feature documentation](https://github.com/xklob/prism/blob/97c0acf8e9c794d6925ed279bc660b3153f1f4ba/README.md)
- **[P2]** [Current scene definitions](https://github.com/xklob/prism/blob/97c0acf8e9c794d6925ed279bc660b3153f1f4ba/app/src/main/java/com/caleb/prism/Scene.kt)
- **[P3]** [Song-service implementation](https://github.com/xklob/prism/blob/97c0acf8e9c794d6925ed279bc660b3153f1f4ba/app/src/main/java/com/caleb/prism/SongServices.kt)
- **[P4]** [File-analysis implementation](https://github.com/xklob/prism/blob/97c0acf8e9c794d6925ed279bc660b3153f1f4ba/app/src/main/java/com/caleb/prism/LocalFileAnalyzer.kt)
- **[P5]** [Crowd-controller documentation](https://github.com/xklob/prism/blob/97c0acf8e9c794d6925ed279bc660b3153f1f4ba/controller/README.md)
- **[P6]** [Repository tree](https://github.com/xklob/prism/tree/97c0acf8e9c794d6925ed279bc660b3153f1f4ba), [model/runtime notices](https://github.com/xklob/prism/blob/97c0acf8e9c794d6925ed279bc660b3153f1f4ba/app/src/main/assets/rhythm/NOTICE.txt), and [reported release validation](https://github.com/xklob/prism/blob/97c0acf8e9c794d6925ed279bc660b3153f1f4ba/docs/releases/1.15.0.md)

