# Implementation Notes

## Priority order

The first engineering goal is not AI integration. It is proving the AirPods interaction layer is reliable enough to build on.

Prioritize:

1. Motion stream reliability
2. Neutral-pose tracking
3. False-positive-resistant gesture recognition
4. Locked-phone and background behavior
5. AirPods remote-control behavior
6. Voice hold
7. Action Button startup
8. Conversation state machine
9. AI integration
10. Spatial menu

## Product constraints

- Keep the permanent gesture vocabulary small.
- Prefer false negatives over false positives.
- Never execute an external action from a head gesture alone.
- Voice hold must remain available as the universal freeform escape hatch.
- The spatial menu is temporary and announced each time.
- The user should not need to look at the phone during a normal active session.

## Open hardware questions

- Which AirPods models expose sufficiently reliable motion data for all proposed gestures?
- Does motion streaming remain available under the required lock-screen/background conditions?
- Which AirPods press patterns reach the app through public iOS APIs while our audio session is active?
- How do press mappings interact with user-configured AirPods settings?
- How quickly can the app switch between AI playback and microphone capture without audible friction?

These questions should be answered by instrumentation and device testing before product behavior is finalized.
