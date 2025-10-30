# ADR-001 — Interaction Model & Popup Design

## Context
Early prototypes risked accidental taps and inconsistent gestures (tap vs hold vs swipe).  
Goal → simplify to one conscious, ergonomic interaction.

## Decision
Adopt a **Tap → Contextual Popup** model.

### Key Behaviors
- Tap opens **floating bubble** with three grouped actions:  
  `🤍 | 😊 😐 😞 | 💬`
- Popup position adapts to finger Y-coordinate  
  - Top half → below row  
  - Bottom half → above row  
- Slight backdrop blur; scroll locked until dismissed.  
- Actions give medium haptic feedback and auto-close popup.  
- “Undo” toast (5 s) allows safe reversal.

### Rationale
- Prevents accidental sends.  
- One-hand-friendly (thumb reach logic).  
- Combines all intents in one discoverable gesture.  
- Encourages deliberate, mindful check-ins.

### Alternatives Considered
| Option | Rejected Because |
|---------|------------------|
| Instant Tap Send | Too easy to mis-tap; no anticipation moment. |
| Hold + Swipe | Complex; poor discoverability. |
| Dedicated Buttons | Too heavy visually; breaks flow. |

### Consequences
- Slightly slower first action, higher emotional satisfaction.  
- Requires overlay coordination to prevent scroll flicker.  
- Future-proof: same popup can host Pro features (custom emojis, sounds).

### Status
**Accepted — Interaction SoT v0 Implementation Baseline**

### References
- Interaction-SoT-v0.md  
- Visual-SoT-v0.md
