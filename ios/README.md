# iOS Implementation Area

This directory is reserved for the native iOS project.

Planned initial structure:

```text
ios/
  AirPodsSilentAI.xcodeproj
  AirPodsSilentAI/
    App/
    Motion/
    Audio/
    Voice/
    Conversation/
    Intents/
    UI/
  AirPodsSilentAITests/
```

## First build target

The first Xcode target should be the gesture laboratory described in `../docs/ROADMAP.md`.

Do not start with full AI integration. First prove:

1. AirPods motion streaming works reliably.
2. Neutral pose tracking works while sitting, standing, and walking.
3. Nod, shake, left, right, up, down, and down-left hold can be classified with very low false-positive rates.
4. Required behavior remains usable with the phone locked and in a pocket where iOS permits it.
5. AirPods remote-control events are understood on the exact target hardware.

Once those assumptions are proven, reuse the same motion and interaction layers in the production app.
