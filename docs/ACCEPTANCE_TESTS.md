# Acceptance Tests

## Hardware connection

- Supported AirPods connect and report headphone motion.
- Motion interruption or disconnect is surfaced without crashing.
- Reconnect restores a usable session or clearly asks the user to restart.

## Gesture recognition

- Nod produces `YES`.
- Shake produces `NO`.
- Right produces `NEXT`.
- Left produces `BACK`.
- Chin up produces `MORE`.
- Chin down produces `TRY_AGAIN`.
- Down-left hold enters voice recording only after the hold threshold.
- Returning out of the down-left zone stops recording.
- Natural walking and looking around do not regularly trigger commands.

## Conversation behavior

- AI asks one actionable question at a time.
- `MORE` adds useful detail without restarting the whole answer.
- `TRY_AGAIN` produces a materially different option.
- `INTERRUPT` stops speech immediately without rejecting the current topic.
- External actions cannot execute from a nod alone.
- Spatial-menu mappings are always spoken before directional selection is enabled.

## Voice escape

- Start earcon plays when recording begins.
- Stop earcon plays when recording ends.
- Recorded speech is transcribed and submitted as the next user turn.
- Recording does not begin from a brief glance down-left.

## Session lifecycle

- iPhone Action Button can start or resume the app through an App Intent.
- The normal interaction can proceed without looking at the phone after startup.
- End-session control stops playback, saves state, and releases active resources.

## Hardware-dependent controls

The proposed AirPods remote-control mappings are accepted only after they are confirmed on the target hardware and iOS version.

If iOS exposes a different set of reliable remote commands, the product mapping must be updated while preserving these semantic operations:

- Interrupt
- Execute
- End session
