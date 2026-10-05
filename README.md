# AirPods Silent AI

> **Retired prototype / reference only.** The active Silent AIVA implementation now lives in [banddude/officeadmin-ios](https://github.com/banddude/officeadmin-ios). Remaining AirPods motion/gesture validation is tracked in officeadmin-ios#24, with UI/session/voice work in #19, #22, and #23. Do not start new product work in this repository; it is retained only for historical specs until archival.

A hands-free iOS interaction system for controlling AI conversations with AirPods head motion, AirPods controls, and an optional iPhone Action Button.

The goal is to let a user carry on a useful AI interaction without looking at the phone, typing, or continuously speaking.

## Core idea

The AI speaks through AirPods. The user responds with a small, fixed interaction language:

| Input | Meaning |
| --- | --- |
| Nod | Yes |
| Shake | No |
| Right | Next |
| Left | Back |
| Chin up | More |
| Chin down | Try something else |
| Down-left + hold | Record freeform voice input |
| AirPod single press | Interrupt |
| AirPod double press | Approve / execute |
| AirPod triple press | End session |
| iPhone Action Button | Start / resume session |

The fixed gesture set stays intentionally small. When a task needs more choices, the AI can temporarily present a spoken spatial menu and map directions to options.

## Why this works

The physical interface has very low bandwidth, so the AI adapts the conversation to it. Instead of asking broad open-ended questions, it turns work into short decisions, gives a recommendation first, and progressively reveals more detail only when requested.

A nod means agreement. A deliberate AirPods execution command means actually perform the external action.

If gestures are not expressive enough, holding down-left opens a push-to-talk style voice input. Recording continues only while the head remains in that position, then the transcript becomes the next AI message.

## Technical direction

The project is expected to use:

- Core Motion and `CMHeadphoneMotionManager` for AirPods head-motion data
- A custom gesture recognizer for nod, shake, directional movement, and hold gestures
- `MPRemoteCommandCenter` where supported for AirPods media-control events
- App Intents for Start / Resume Session and iPhone Action Button integration
- AVFoundation for audio routing, playback, recording, and interruption
- Speech transcription for freeform voice input
- A conversation state machine optimized for silent interaction

## Repository docs

- [Product and engineering spec](SPEC.md)
- [Architecture](docs/ARCHITECTURE.md)
- [Interaction model](docs/INTERACTION_MODEL.md)
- [Prototype roadmap](docs/ROADMAP.md)

## Status

Concept and specification stage. The first implementation target is a gesture laboratory that proves reliable AirPods gesture recognition before building the complete AI conversation layer.
