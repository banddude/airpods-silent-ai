# Interaction Model

## Design rule

The base gesture language is permanent and small. The AI adapts the conversation to that language instead of asking the user to memorize a large gesture vocabulary.

## Permanent inputs

| Input | Semantic event | Purpose |
| --- | --- | --- |
| Nod | `YES` | Agree / select yes |
| Shake | `NO` | Reject / answer no |
| Right | `NEXT` | Move forward |
| Left | `BACK` | Move backward |
| Chin up | `MORE` | Expand current answer |
| Chin down | `TRY_AGAIN` | Offer a different approach |
| Down-left hold | `VOICE_HOLD` | Temporary freeform speech |
| AirPods press | `INTERRUPT` | Stop current AI speech |
| AirPods deliberate execute control | `EXECUTE` | Perform pending external action |
| AirPods end control | `END_SESSION` | End session |
| iPhone Action Button | `START_OR_RESUME` | Enter silent mode |

The exact AirPods remote-control gesture used for `EXECUTE` and `END_SESSION` must be proven on real hardware before those mappings are treated as final.

## Yes versus execute

This distinction is fundamental.

`YES` means:

> I agree with that choice or recommendation.

`EXECUTE` means:

> Perform the pending external action now.

Example:

AI: "I recommend sending the shorter reply. Agree?"

User nods.

AI: "Ready to send."

User performs the execute control.

Only then is the message sent.

## No versus try again

These also have intentionally different meanings.

`NO` means the proposed statement, choice, or action is rejected.

`TRY_AGAIN` means the user wants another solution without needing to explain what was wrong.

Examples of `TRY_AGAIN`:

- Rewrite it differently
- Give another recommendation
- Find another route
- Present another option
- Reconsider the reasoning

## Progressive disclosure

Default AI responses should be short.

`MORE` increases detail one level at a time.

The AI should not repeat everything it already said unless necessary. It should add the next useful layer of context.

## Interrupt

Interrupt should be immediate.

When the user interrupts:

1. Stop speech playback.
2. Preserve the current semantic context.
3. Do not interpret interruption as rejection.
4. Remain ready for another base gesture or voice hold.

An interrupt is equivalent to "stop talking for a second," not "cancel this topic."

## Voice hold

Voice hold is the universal escape hatch.

The user enters the down-left hold zone and remains there while speaking. Leaving the zone ends the recording.

The resulting transcript is treated as a normal freeform user turn.

This allows the permanent gesture set to stay small because any unusual request can still be expressed without touching the phone.

## Spatial menu

Spatial menus are temporary and spoken.

Example:

AI: "Quick menu. Left: reply. Right: remind me later. Up: hear context. Down: archive."

During that menu only, those directions select the announced options.

After a selection, the permanent mapping returns.

### Rules

- Never require the user to memorize spatial-menu mappings.
- Announce every mapping before enabling it.
- Prefer two or three options when possible.
- Use four only when useful.
- Do not place destructive actions directly in a spatial menu without a separate execute step.
- Exit the menu after one selection unless the AI explicitly says it remains open.

## Spoken-response style

Silent mode should use a dedicated prompting policy.

Prefer:

- Recommendation first
- One decision at a time
- Short answer first
- Explicit choices
- Natural confirmation
- Minimal filler

Avoid:

- Long setup before the decision
- Multiple unanswered questions in one turn
- Lists of many choices without a spatial menu
- Repeating gesture instructions during normal use
- Speaking system-state details unless something failed

## Example session

AI: "You have three things that need decisions. First is a customer reply. Want the summary?"

User nods.

AI: "They want a return visit. I recommend treating it as a normal billable service call. Agree?"

User nods.

AI: "Ready to send that direction to the office."

User executes.

AI: "Sent. Next item is an estimate at $1,500."

User raises chin.

AI: "That price covers the expected labor and markup and is still reasonable for the scope."

User lowers chin.

AI: "Alternative: quote $1,350 with a tighter scope and bill changes separately."

User performs down-left hold and says: "Keep it at fifteen hundred but don't make it sound padded."

AI receives the transcript and continues from that instruction.

## Principle

The user should feel as though they are controlling a conversation, not operating a gesture-driven menu system.

The gestures are primitives. The AI is responsible for turning complex work into interactions those primitives can express.
