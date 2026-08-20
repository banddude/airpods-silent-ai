# First Build Tasks

## 1. Hardware validation

- Verify `CMHeadphoneMotionManager` on target AirPods.
- Measure lock-screen and background behavior.
- Verify AirPods disconnect/reconnect behavior.
- Determine which remote-control events reach the app.

## 2. Gesture lab

- Show live pitch, yaw, roll, and angular velocity.
- Implement neutral-pose calibration and slow drift.
- Record and replay motion traces.
- Show recognized gesture and confidence.

## 3. Core gestures

- Nod -> Yes
- Shake -> No
- Right -> Next
- Left -> Back
- Chin up -> More
- Chin down -> Try again
- Down-left hold -> Voice input

## 4. Audio and voice

- AI/spoken prompt playback.
- Immediate interrupt.
- Recording start/stop earcons.
- Down-left push-to-talk recording.
- Transcription handoff.

## 5. Session controls

- Action Button start/resume App Intent.
- Validated AirPods execute command.
- Validated AirPods end-session command.

## 6. Conversation layer

- State machine.
- Progressive disclosure.
- Yes versus execute safety boundary.
- Temporary spatial menu.
- Real AI integration after the interaction layer is proven.
