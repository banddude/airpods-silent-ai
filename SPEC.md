# AirPods Silent AI
## v1 Product and Engineering Spec

## 1. Product concept

Build an iOS conversational AI interface designed to be operated primarily through AirPods without looking at the phone, typing, or speaking for routine interactions.

The AI speaks through the AirPods. The user responds using:

- Head gestures detected from AirPods motion sensors
- AirPods stem presses where iOS exposes usable remote-control events
- Optional short voice recording when freeform input is necessary
- iPhone Action Button to start or resume the experience

The normal interaction should remain intentionally small and predictable. More complicated choices can temporarily use a spoken spatial menu.

The user should be able to keep the iPhone locked and in a pocket for as much of the experience as iOS permits.

## 2. Core interaction language

| User input | Base meaning |
| --- | --- |
| Nod | Yes |
| Shake head | No |
| Turn or tilt right | Next |
| Turn or tilt left | Back |
| Chin up | More |
| Chin down | Try something else |
| Down-left + hold | Record freeform voice input |
| AirPod single press | Interrupt |
| AirPod double press | Approve / execute |
| AirPod triple press | End session |
| iPhone Action Button | Start / resume session |

The user should not need to remember different meanings for these inputs depending on context.

## 3. Command semantics

### Nod: Yes

Indicates agreement with what the AI just asked or proposed.

A nod produces `YES`.

A nod does not automatically execute an external action.

### Shake: No

Indicates disagreement, rejection, or a negative answer.

A shake produces `NO`.

### Right: Next

Moves forward through the current sequence.

Examples:

- Next message
- Next task
- Next email
- Next option
- Next project
- Next decision

Produces `NEXT`.

### Left: Back

Returns to the previous conversational item or decision.

Possible behavior:

- Return to previous item
- Repeat the previous decision
- Reopen previous context

Produces `BACK`.

It must not automatically undo an already executed external action.

### Chin up: More

Requests additional information about the current subject.

The AI should progressively reveal detail rather than starting with a long answer.

Produces `MORE`.

### Chin down: Try something else

Requests an alternative rather than merely rejecting the current answer.

This is intentionally different from `NO`.

Depending on context, it can mean:

- Another recommendation
- Another wording
- Another solution
- Another option
- Different reasoning

Produces `TRY_AGAIN`.

## 4. Freeform voice input

Some thoughts cannot reasonably be expressed using discrete gestures. The user needs a fast escape hatch into normal language.

### Gesture

Move the head down and left, then hold.

After the gesture has remained inside the recording zone for approximately 500 milliseconds:

1. Play a subtle recording-start earcon.
2. Begin microphone recording.
3. Continue recording while the user's head remains inside the down-left zone.
4. When the head leaves the zone, stop recording.
5. Play a subtle recording-stop earcon.
6. Transcribe the recording.
7. Insert the transcription into the conversation as the user's next message.
8. Resume silent interaction mode.

### Recording safeguards

Recording must not begin from simply glancing down-left.

Require:

- Deliberate directional threshold
- Minimum hold duration
- Stable head position
- Gesture confidence threshold

The system should never continuously record merely because the user's head naturally happens to point down-left.

## 5. AirPods controls

AirPods controls provide deliberate physical commands that complement head motion.

The exact event mapping available to a third-party iOS app must be validated on target AirPods hardware and iOS versions.

### Single press: Interrupt

Immediately stop AI speech without ending the session.

Produces `INTERRUPT`.

The system should remain ready for another gesture after interruption.

### Double press: Approve / execute

This is the deliberate action command.

Produces `EXECUTE`.

Examples:

- Send the message
- Submit the response
- Save the change
- Perform the approved action
- Confirm the current choice

Important distinction:

- Nod means agreement.
- Double press means perform it.

### Triple press: End session

Ends the active silent AI session.

Produces `END_SESSION`.

Expected behavior:

1. Stop current audio.
2. Save conversation state.
3. Stop motion monitoring when appropriate.
4. End the active interaction session.
5. Play a short completion earcon.

No long spoken goodbye.

## 6. iPhone Action Button

The iPhone Action Button is the primary entry point through an App Intent / App Shortcut.

Default action: `Start Silent AI`.

If no session exists:

- Start a new session.
- Confirm supported AirPods are available.
- Begin head-motion monitoring.
- Play a short ready sound.
- Begin the AI interaction.

If a resumable session exists:

- Resume it.

If the session is already active:

- Return it to the active interaction state.

The Action Button should not normally end the session. The AirPods end command handles that.

## 7. Neutral position

The system needs a continuously maintained neutral head position.

When a session begins:

1. Read current AirPods orientation.
2. Establish a neutral center.
3. Continuously compensate for slow changes in posture.
4. Detect deliberate motion relative to that neutral position.

The user should not need to stare perfectly straight ahead.

Neutral should drift slowly over time while quick intentional movements are recognized as gestures.

## 8. Gesture recognition

AirPods motion data should be read through Core Motion headphone-motion APIs.

Gesture recognition should use a combination of:

- Orientation
- Angular velocity
- Movement direction
- Duration
- Return toward neutral
- Peak displacement
- Gesture confidence
- Recent gesture history

Do not trigger commands from a single orientation threshold alone.

For example, a nod should resemble:

`neutral -> pitch excursion -> reversal -> return toward neutral`

rather than:

`head happens to be pointed downward`

False negatives are preferable to false positives.

## 9. Gesture cooldown

After recognizing a gesture, briefly suppress recognition of the same movement.

Initial target:

`300 to 600 ms`

Tune experimentally.

The recognizer should also wait for the user's head to substantially return toward neutral before recognizing another directional command.

## 10. Audio feedback

Avoid spoken system confirmations whenever possible.

Use a small set of recognizable earcons for:

- Session ready
- Gesture accepted when useful
- Recording started
- Recording stopped
- Action executed
- Error
- Session ended

The AI itself handles semantic feedback.

## 11. Conversational behavior

The model must know that the user is operating in Silent Interaction Mode.

It should:

- Ask one decision at a time
- Prefer yes/no questions when appropriate
- Present a recommendation before asking for a decision
- Keep spoken responses short by default
- Offer deeper information only when requested
- Avoid broad questions when a smaller decision can accomplish the same thing
- Use explicit choices when ambiguity exists
- Pause after actionable questions
- Understand the base gesture vocabulary
- Avoid forcing confirmation for trivial navigation
- Require deliberate execution for consequential actions

## 12. Progressive disclosure

Spoken answers should begin concise.

Repeated `MORE` commands progressively expand the answer.

Example:

Level 1:

> I recommend $1,500.

More.

Level 2:

> That covers the expected labor and material markup.

More.

Level 3:

> The labor assumption is approximately eight hours...

The user controls how much detail they consume.

## 13. Spatial menu

Spatial menus are optional and only used when the fixed interaction language is insufficient.

They do not replace the base commands.

The AI announces a temporary directional mapping.

Example:

> Four options. Left is reply. Right is remind me later. Up is more context. Down is archive.

The user moves their head in the corresponding direction.

After selection, the temporary mapping disappears and normal base controls resume.

The user should never need to memorize spatial-menu assignments. Every mapping must be spoken immediately before selection.

## 14. Spatial-menu trigger

The AI can offer a spatial menu automatically when several equally valid choices exist.

Example:

> There are four reasonable options. Want the quick menu?

A nod opens it.

Do not add another permanent gesture until usage demonstrates a need.

## 15. Conversation state machine

At minimum, maintain these states:

### IDLE

No active session. Action Button starts.

### SPEAKING

AI is speaking.

Available commands can include interrupt, more, next, back, and voice recording when semantically appropriate.

### AWAITING_RESPONSE

AI has asked for input. Base gestures are available.

### RECORDING

Down-left hold is active. Leaving the recording zone stops recording.

### TRANSCRIBING

Voice input is being converted to text.

### AWAITING_EXECUTION

The AI has an external action ready. Nod can indicate agreement. The deliberate execution command performs it.

### SPATIAL_MENU

Directions temporarily map to spoken menu choices.

### ENDED

Session state saved. Motion and audio resources released.

## 16. Action safety model

Actions should be divided into three categories.

### Navigation

Examples:

- Next
- Back
- More
- Try again

No confirmation required.

### Conversational decisions

Examples:

- Yes
- No
- Choose an option

Head gesture is sufficient.

### External actions

Examples:

- Send message
- Send email
- Submit form
- Delete something
- Approve something
- Change external data

Require explicit execution.

Default execution input: AirPods double press if the target hardware and iOS path expose it reliably.

The AI should clearly state what will happen immediately before entering the execution state.

## 17. Error handling

If gesture confidence is low, do nothing.

If the system repeatedly cannot recognize a gesture, the AI may briefly say:

> I didn't get that.

Then reopen the response window.

Do not guess.

## 18. Response windows

Gesture interpretation should be context-aware.

When the AI asks a question:

1. Finish speaking.
2. Enter a response-ready state.
3. Increase gesture sensitivity.
4. Wait for user input.

While the user is simply listening or moving normally, head motion should have much less ability to issue consequential commands.

## 19. Technical architecture

High-level flow:

```text
AirPods motion sensors
        |
CMHeadphoneMotionManager
        |
Gesture Recognition Engine
        |
Interaction State Machine
        |
Conversation Controller
        |
AI
        |
Speech / streamed audio
        |
AirPods
```

Additional inputs:

```text
AirPods remote controls
        |
MPRemoteCommandCenter
        |
Interaction State Machine
```

```text
iPhone Action Button
        |
App Intent
        |
Start / Resume Session
```

## 20. Major app components

### HeadMotionService

Responsible for:

- Connecting to headphone motion data
- Neutral orientation
- Motion samples
- Calibration
- AirPods availability

### GestureRecognizer

Produces semantic events such as:

```text
YES
NO
NEXT
BACK
MORE
TRY_AGAIN
VOICE_HOLD_STARTED
VOICE_HOLD_ENDED
```

### RemoteControlService

Produces available accessory events that may map to:

```text
INTERRUPT
EXECUTE
END_SESSION
```

### VoiceInputService

Handles:

- Microphone recording
- Recording earcons
- Speech transcription
- Transcript delivery

### ConversationController

Maintains:

- AI conversation
- Current item
- Current question
- Available actions
- Execution state
- Spatial menu

### AudioSessionManager

Coordinates:

- AI playback
- Interruption
- Microphone recording
- AirPods routing
- System audio session

### SilentModePromptingLayer

Tells the AI:

- Which inputs are currently possible
- Current interaction state
- Whether an action requires execution
- How concise spoken responses should be

## 21. MVP sequence

### Prototype 1: AirPods gesture laboratory

Display:

- Current pitch
- Current yaw
- Current roll
- Neutral position
- Recognized gesture
- Confidence

Recognize:

- Nod
- Shake
- Right
- Left
- Up
- Down
- Down-left hold

Test while:

- Standing
- Sitting
- Walking
- Phone in hand
- Phone in pocket
- Screen locked where iOS permits

### Prototype 2: Basic spoken conversation

The app asks simple questions and confirms recognized gestures.

Target: near-zero false positives during ordinary movement.

### Prototype 3: Voice escape

AI asks a question. User performs down-left hold, speaks, returns to neutral, and the transcript becomes the next user message.

### Prototype 4: AirPods press controls

Verify on target AirPods hardware that iOS delivers the expected remote commands for the intended single, double, and triple press mappings.

This must be treated as a hardware-validation item rather than assumed behavior.

### Prototype 5: Action Button

Create a Start Silent Session App Intent and verify the session can be launched or resumed from the iPhone Action Button.

## 22. MVP acceptance criteria

The MVP succeeds when the user can:

1. Put in supported AirPods.
2. Press the iPhone Action Button.
3. Hear the AI begin a session.
4. Answer Yes with a nod.
5. Answer No with a shake.
6. Move Next with right.
7. Move Back with left.
8. Request More with chin-up.
9. Request a different answer with chin-down.
10. Interrupt speech with an AirPods control.
11. Approve an external action with a deliberate AirPods command if validated on target hardware.
12. End the session with the intended AirPods command if validated on target hardware.
13. Hold down-left and speak an arbitrary response.
14. Return the head toward neutral to stop and submit voice input.
15. Complete the normal flow without looking at or touching the phone after startup.

## 23. Core design principle

The gesture vocabulary should stay small.

The intelligence belongs in the conversation layer.

The system should not attempt to create dozens of permanent physical gestures. Instead, the AI should restructure complicated tasks into decisions that can usually be answered with:

- Yes
- No
- Next
- Back
- More
- Try something else

When those are insufficient, the user can either:

- Hold down-left and speak freely
- Enter a temporary spoken spatial menu

That keeps the physical interface simple while allowing the AI interaction itself to remain effectively unlimited.
