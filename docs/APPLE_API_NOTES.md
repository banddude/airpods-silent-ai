# Apple API Notes

This document tracks Apple-platform assumptions that must be validated against current SDK behavior and real hardware.

## Headphone motion

Primary API:

- `CMHeadphoneMotionManager`

Use it to obtain supported headphone motion data and derive relative head orientation and motion patterns.

Product assumption:

The app will recognize nod, shake, directional movement, and a down-left hold from the underlying motion stream rather than relying on Apple's private/system-level Siri head-gesture classifier.

## Audio and remote controls

Relevant APIs:

- `AVAudioSession`
- `MPRemoteCommandCenter`

Important limitation:

The proposed AirPods single-press, double-press, and triple-press product mappings are not considered proven until tested with the target AirPods model, current iOS version, and the app's real audio-session configuration.

The implementation must adapt the product mapping if iOS does not expose all three events distinctly and reliably.

## Action Button

Relevant APIs:

- App Intents
- App Shortcuts

Expose a narrow `Start Silent Session` action that can be assigned to the iPhone Action Button.

## Voice capture

Relevant APIs:

- `AVAudioSession`
- `AVAudioEngine` or another appropriate AVFoundation recording path
- Speech transcription implementation selected during development

The recording state must coexist cleanly with AirPods routing and AI playback.

## Background and lock-screen behavior

Treat all background assumptions as experimental until verified on device.

Tests must document behavior when:

- The screen locks
- The app backgrounds
- Audio playback remains active
- Headphone motion streaming is active
- Voice capture starts and stops
- AirPods disconnect and reconnect

The product should be designed around behavior Apple publicly supports, not accidental behavior observed in one OS build.

## Primary Apple references

- Core Motion headphone motion: https://developer.apple.com/documentation/coremotion/cmheadphonemotionmanager
- MediaPlayer remote commands: https://developer.apple.com/documentation/mediaplayer/mpremotecommandcenter
- App Intents: https://developer.apple.com/documentation/appintents
- AVFoundation audio: https://developer.apple.com/av-foundation/
