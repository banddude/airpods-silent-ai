# Prototype Roadmap

## Phase 0: Hardware validation

Before building the full AI experience, prove the assumptions that depend on Apple hardware and iOS behavior.

### Validate headphone motion

- Confirm target AirPods model reports motion through `CMHeadphoneMotionManager`
- Record pitch, yaw, roll, rotation rate, gravity, and user acceleration
- Test while phone is locked
- Test while phone is in pocket
- Test while walking
- Test route changes and AirPods reconnects

### Validate AirPods controls

Determine exactly which remote commands the target AirPods model generates while the app owns the active audio session.

Test proposed mappings:

- Single press -> Interrupt
- Double press -> Execute
- Triple press -> End session

Do not lock product behavior to these mappings until confirmed on device.

### Validate background behavior

Measure what remains available when:

- Screen turns off
- App is backgrounded
- Audio playback is active
- Recording begins
- Recording ends

Document any iOS limitations before proceeding.

## Phase 1: Gesture laboratory

Build a minimal SwiftUI app with no AI dependency.

Display:

- AirPods connection state
- Pitch
- Yaw
- Roll
- Angular velocity
- Current neutral pose
- Current recognizer state
- Last accepted gesture
- Confidence score

Implement:

- Recenter
- Start / stop motion capture
- Motion trace recording
- Motion trace replay

First gestures:

1. Nod
2. Shake
3. Right
4. Left
5. Chin up
6. Chin down
7. Down-left hold

Success criterion:

Ordinary walking and looking around should produce essentially no unintended accepted gestures.

## Phase 2: Gesture tuning

Collect multiple examples of each gesture under real-world conditions.

Tune:

- Minimum angle
- Minimum / maximum duration
- Velocity requirements
- Return-to-neutral rules
- Cooldown
- Confidence threshold
- Neutral drift speed

Add replayable test fixtures for successful gestures and known false-positive movements.

## Phase 3: Audio interaction

Add:

- AirPods audio route management
- Short earcons
- Spoken prompts
- Immediate interrupt
- Response-ready windows

Prototype conversation:

1. App asks a yes/no question.
2. User nods or shakes.
3. App confirms naturally.
4. User navigates next/back.
5. User requests more or try again.

No network AI is required yet.

## Phase 4: Voice hold

Implement down-left hold as push-to-talk.

Requirements:

- 500 ms approximate activation hold
- Start earcon
- Record only while the hold remains active
- Stop immediately when the user leaves the zone
- Stop earcon
- Transcribe
- Submit transcript as a user turn
- Return to silent mode

Test false activation extensively.

## Phase 5: Action Button

Implement a `Start Silent Session` App Intent and expose it through App Shortcuts.

Validate:

- Start with locked phone
- Resume existing session
- Handle unsupported / disconnected AirPods gracefully

## Phase 6: Conversation state machine

Implement:

- Idle
- Speaking
- Awaiting response
- Recording
- Transcribing
- Awaiting execution
- Spatial menu
- Ended

Use a local fake AI first so every state transition can be tested deterministically.

## Phase 7: AI integration

Connect a conversational AI backend.

The AI receives:

- Conversation history
- Current state
- Available gesture events
- Pending action information
- Progressive-disclosure level

The AI should return structured data alongside spoken text so the app knows whether it is:

- Informing
- Asking yes/no
- Offering choices
- Waiting for execution
- Opening a spatial menu

## Phase 8: External actions

Add a generic pending-action model.

Require deliberate execute input before any action leaves the conversation.

Start with a harmless demo action such as saving a note locally before integrating messaging, email, or other external services.

## Phase 9: Spatial menu

Add temporary spoken directional menus.

Test:

- 2-choice menu
- 3-choice menu
- 4-choice menu
- Interrupt during menu
- Voice hold during menu
- Selection followed by external execution

## Phase 10: Real-world trial

Run complete sessions while:

- Walking
- Sitting at a desk
- Driving only in a safe, non-distracting test context where interaction is lawful and appropriate
- Phone locked in pocket
- Listening for extended periods
- Switching between silent gestures and voice hold

Track:

- False positives
- Missed gestures
- Accidental recordings
- Average response latency
- Number of times the user needs the phone screen
- Number of times freeform voice input is needed
- Which permanent gestures are rarely used

## MVP definition

MVP is complete when a user can start from the iPhone Action Button, conduct a useful AI conversation through AirPods using the six core semantic gestures, inject arbitrary speech with down-left hold, deliberately execute an external action, and end the session without looking at the phone during the normal flow.
