# Architecture

## Goal

Separate hardware sensing, gesture classification, conversation state, audio, and AI behavior so each layer can be tested independently.

## Proposed modules

```text
AirPodsSilentAI/
  App/
    AirPodsSilentAIApp.swift
    AppRouter.swift
  Motion/
    HeadMotionService.swift
    NeutralPoseTracker.swift
    GestureRecognizer.swift
    GestureModels.swift
  Audio/
    AudioSessionManager.swift
    RemoteControlService.swift
    EarconPlayer.swift
  Voice/
    VoiceInputService.swift
    TranscriptionService.swift
  Conversation/
    ConversationController.swift
    InteractionState.swift
    InteractionEvent.swift
    SpatialMenu.swift
    SilentModePromptBuilder.swift
  Intents/
    StartSilentSessionIntent.swift
    AppShortcuts.swift
  UI/
    GestureLabView.swift
    SessionDebugView.swift
```

The first prototype does not need every module. The structure is intended to keep the gesture lab code reusable when the AI layer arrives.

## Event model

Hardware-specific events should be converted into semantic interaction events before reaching the conversation controller.

Example:

```swift
enum InteractionEvent {
    case yes
    case no
    case next
    case back
    case more
    case tryAgain
    case interrupt
    case execute
    case endSession
    case voiceHoldStarted
    case voiceHoldEnded
    case transcript(String)
}
```

The conversation layer should not need to know whether `yes` came from a nod, a UI test button, or a future accessibility input.

## Motion pipeline

```text
CMHeadphoneMotionManager
        |
raw CMDeviceMotion samples
        |
NeutralPoseTracker
        |
relative pitch / yaw / roll + angular velocity
        |
GestureRecognizer
        |
confidence-scored candidate
        |
InteractionEvent
```

### NeutralPoseTracker

Responsibilities:

- Capture an initial neutral pose at session start
- Express current orientation relative to neutral
- Allow slow neutral drift when the user changes posture
- Freeze or greatly reduce drift while a deliberate gesture is in progress
- Recenter explicitly if tracking becomes unreliable

### GestureRecognizer

Do not classify a gesture from a single angle threshold.

Each gesture should have phases such as:

1. Armed at neutral
2. Excursion begins
3. Minimum amplitude crossed
4. Expected velocity or direction change observed
5. Return toward neutral
6. Gesture accepted
7. Cooldown

The down-left voice gesture differs because it is a hold zone rather than a return gesture.

## Gesture confidence

Each recognizer should return a confidence score based on:

- Peak angular displacement
- Angular velocity
- Gesture duration
- Direction purity
- Return-to-neutral quality
- Interference from other axes
- Whether the interaction state currently expects a response

If confidence is below threshold, emit nothing.

## Interaction state machine

Suggested state:

```swift
enum InteractionState {
    case idle
    case speaking
    case awaitingResponse
    case recording
    case transcribing
    case awaitingExecution
    case spatialMenu
    case ended
}
```

The current state controls which gestures are meaningful and how aggressively the recognizer should listen for them.

For example, a nod while `awaitingResponse` may be accepted at normal confidence, while an incidental nod during long-form playback should not cause an external action.

## Audio architecture

`AudioSessionManager` should be the single owner of `AVAudioSession` policy.

It coordinates transitions between:

- AI playback
- Microphone capture
- Interruption
- Audio route changes
- AirPods disconnect / reconnect

Voice recording should stop AI playback first. Returning from recording should restore the prior playback/session configuration before submitting the transcript.

## AirPods remote controls

Use `MPRemoteCommandCenter` only for controls iOS actually exposes to the app during the active audio session.

Do not hard-code the proposed single/double/triple press semantics until they are validated on the target AirPods model and iOS version.

The remote-control layer should map whatever reliable events are available into semantic commands such as `interrupt`, `execute`, and `endSession`.

## Conversation controller

The controller owns:

- Current AI turn
- Current interaction state
- Pending external action
- Available choices
- Spatial-menu mapping
- Progressive-disclosure level
- Session resume data

It receives only semantic `InteractionEvent` values.

## Execution safety

External actions should have an explicit pending-action object.

Example:

```swift
struct PendingAction {
    let id: UUID
    let spokenSummary: String
    let requiresExecution: Bool
    let execute: @Sendable () async throws -> Void
}
```

A nod can accept a recommendation without invoking the closure. The deliberate execute command invokes it only while the state is `awaitingExecution`.

## Spatial menu

A spatial menu is temporary state, not a second permanent gesture vocabulary.

Example model:

```swift
struct SpatialMenu {
    var left: MenuChoice?
    var right: MenuChoice?
    var up: MenuChoice?
    var down: MenuChoice?
}
```

The AI announces the mapping immediately before the system enters `spatialMenu` state.

## App Intent

Expose one narrow system action first:

`Start Silent Session`

It should route through one app-level session coordinator rather than duplicating startup logic inside the intent.

## Testing strategy

### Unit tests

- Gesture phase transitions
- Neutral drift
- Cooldown behavior
- False-positive rejection
- State-machine transitions
- Execution gating

### Recorded motion fixtures

The gesture lab should be able to save anonymized motion traces locally for test fixtures.

A recorded trace can then be replayed through `GestureRecognizer` without needing AirPods connected during every test run.

### Device tests

Must include:

- Sitting
- Standing
- Walking
- Looking around naturally
- Phone locked
- Phone in pocket
- AirPods disconnect and reconnect
- Voice hold while moving
- Remote-control behavior on the exact target AirPods model

## Implementation order

1. Motion streaming
2. Neutral pose tracking
3. Gesture lab visualization
4. Gesture recognizers
5. Recorded motion fixtures and tests
6. Audio session and earcons
7. Voice hold and transcription
8. Remote-control validation
9. App Intent startup
10. Conversation state machine
11. AI integration
12. Spatial menu
