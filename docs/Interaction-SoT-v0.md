# LuvPing — Interaction Source-of-Truth v0

## Emotional North Star
> “Every action should feel like a small hug — playful, soft, and intentional.”

The app’s comfort comes from rhythm and haptics:
1. **Anticipation** – tap → popup floats up  
2. **Expression** – choose mood/message → color & haptic pulse  
3. **Acknowledgment** – toast appears  
4. **Completion** – fade out → calm return

---

## Core Gestures

| Gesture | Behavior | Feedback |
|----------|-----------|-----------|
| **Tap** | Opens **finger-aware popup** (no instant send) | Light haptic + springy scale-in |
| **Popup** | Floating bubble `🤍 | 😊 😐 😞 | 💬` | Blur backdrop + soft vibration |
| **Select** | Send mood or preset | Medium haptic + pulse ring |
| **Dismiss** | Tap outside → cancel | Fade out + tick |
| **Toast** | “Sent 😊 to Emily — Undo?” (5 s) | Light success haptic |

---

## Popup Placement Logic
- Detect tap **Y-position**
  - **Top half:** show **below** row  
  - **Bottom half:** show **above** row
- Slide inward 8–12 px from screen edges
- Lock scroll while open; tap outside restores

---

## Haptic Map
| Event | Strength | Type |
|--------|-----------|------|
| Popup open | Light | `impactLight` |
| Icon select | Medium | `impactMedium` |
| Dismiss / Undo | Light | `selection` |
| Toast appear | Soft | `notificationSuccess` |

---

## Motion Rules
| Action | Animation | Duration | Curve |
|---------|------------|-----------|--------|
| Popup in | Scale 0.8→1.0 + slide ±10 px | 220 ms | Spring (150, 12) |
| Icon tap | Bounce 1.0→1.1→1.0 | 150 ms | EaseOut |
| Send pulse | Ripple + fade | 250 ms | EaseOutQuart |
| Dismiss | Opacity 1→0 | 180 ms | EaseIn |
| Toast in | Slide-up 10 px | 200 ms | EaseOut |

---

## Notification Copy
- ❤️ “{Name} checked in ❤️”  
- 😊 “{Name} checked in 😊”  
- 💬 “{Name}: ‘Hey, just checking in 💭’”  
- Reminder → “No pings today yet 💭 Tap to send one.”

---

## Accessibility
- Targets ≥ 60 × 60 px  
- Contrast balanced for dim mode  
- Haptics mirror visual states  
- Subtle vibration when popup overlay appears
