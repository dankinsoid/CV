# Plan: Personal Portfolio Site (voidilov.com/cv)

Living draft. We dump ideas here as they appear, then break them down into focused checklists per section.

## Goal

Build a public site that:
- works as an extended version of my CV for HR / recruiters,
- lets technical interviewers and peers dive deeper into specific topics (articles, demos),
- demonstrates that I can work not only with iOS but also with web tooling.

## Concept

- Hosting: GitHub Pages.
- Domain: `voidilov.com/cv`.
- Engine: Docusaurus — doubles as a demonstration that I can work with such tools.
- Format: a hybrid "CV + blog". Part of the content targets HR (short, overview-style), part targets engineers (long-form deep-dives).

## Content (rough list)

- [ ] The CV itself (extended version).
- [ ] Links to my GitHub projects with short descriptions.
- [ ] Visual iOS demos: a selection of animations I built over my career.
  - [ ] Problem: animations were built inside products of different companies → need to abstract them away from those brands (re-skin, replace assets, keep only the essence of the animation/mechanic).
- [ ] Gradient shader demos + an article about them.
- [ ] Blog:
  - [ ] HR-facing section: short pieces about experience, projects, role.
  - [ ] Technical section: deep-dives (animations, shaders, architecture decisions).

## Open questions

- [ ] How exactly are iOS demos surfaced? Videos? WebGL ports? Flutter ports? Screenshots + write-ups?
  - Considered: "original on iOS + Flutter port for interactive web demo + link to iOS source in this repo".
  - Concerns:
    - Risk of diluting the iOS signal for pure-iOS roles (looks cross-platform).
    - Flutter port is not really the iOS implementation — a tech-savvy interviewer will see different rendering paths (Skia/Impeller vs. CoreAnimation/Metal/SwiftUI Canvas).
    - Double maintenance — the port will likely rot.
  - Leaning towards (in order of preference):
    1. Video of the iOS app + link to iOS source (most honest signal).
    2. WebGL/GLSL port specifically for shaders — same skill, browser-runnable, can't be dismissed as "you're really a Flutter dev".
    3. TestFlight gallery app — strongest "I'm an iOS engineer" signal, but extra ops cost.
    4. Flutter port — only as a fallback when nothing else gets the animation into the browser; label honestly as a separate implementation.
- [ ] Video capture quality (only relevant if we go with the video route):
  - Capture on a real device, not simulator (simulator doesn't reproduce real FPS or Metal/ProMotion behavior).
  - Capture method: QuickTime "New Movie Recording" with the iPhone as source, or Xcode Devices & Simulators screen capture.
  - FPS: master at the device's native rate (120fps on ProMotion, 60fps otherwise). Most viewers will see it at 60Hz anyway, but the master has headroom and renders cleanly on ProMotion Macs.
  - Bitrate matters more than FPS for UI animations — gradients and easing get murdered by low bitrate. Aim for 10+ Mbps H.264, 20+ Mbps for shader demos.
  - Codec: H.264 baseline (universal) + AV1/VP9 alternative source for modern browsers.
  - Alternatives to video for specific cases:
    - Lottie — for UI motion that can be re-exported from After Effects; runs at the viewer's native refresh rate. Can't represent shaders/physics/Metal.
    - APNG / animated WebP — for short looping UI snippets (≤3s).
    - WebGL — preferred over video for shader demos (runs at viewer's refresh rate).
- [ ] Input visualization overlay for interactive demos (gesture- and motion-driven animations are unreadable without it):
  - Preferred: a single overlay `UIWindow` (`windowLevel = .statusBar + 1`) baked into the demo app, combining touch + motion indicators. Build once, reuse for every demo. TouchPosé is touch-only — for motion we need our own.
  - Avoid: filming the device + hand physically (moiré, glare, lower effective resolution); PiP camera of the hand (visually noisy).
  - Touch indicators (from `UIEvent.allTouches`):
    - multi-touch (two dots simultaneously for pinch / rotate);
    - drag — a fading trail to make the trajectory readable;
    - long-press — a growing/ripening indicator so a still finger reads as intentional;
    - velocity throws — make the release moment visually distinct.
  - Motion indicators (from `CMMotionManager` and `UIResponder.motionBegan`):
    - tilt — small HUD (bubble level or 2D arrow following the gravity vector) for parallax / orientation-driven shader demos;
    - shake — brief overlay flash + label when `.motionShake` fires; optionally a magnitude bar from `accelerometerData`;
    - rotation — circular arrow tracking `attitude` / `rotationRate` for gyro-driven demos.
  - Enable the overlay only in a demo build target, never in shipping code.
- [ ] How to "de-brand" the animations without violating NDAs / employer rights.
- [ ] Navigation structure: what does HR see on the first screen vs. what does an engineer see?
- [ ] Language: English / Russian / both?

---

_Last updated: 2026-05-12_
