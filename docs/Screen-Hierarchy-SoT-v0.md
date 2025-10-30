# LuvPing — Screen Hierarchy SoT v0

This document locks the **screens, navigation, and high-level folder plan** so the UI matches our Interaction & Visual SoTs before any code.

---

## 1) App Map (High-Level)

AppShell (Navigation Host)
└── HomeList (default)
├── QuickActionPopup (finger-aware; follows tap)
├── Toast (Undo)
└── FAB: Add Person
└── AddPerson
├── Invite (code/link/QR; confirm connect)
└── Pending/Confirm Modal
└── PersonDetail (later)
├── Streak card (current/best)
└── Vibe history (weekly mood counts)
└── Settings
├── Account (Apple/Google sign-in; delete account)
├── Reminders (global; later per-person)
├── Appearance (Dark & Cozy presets; theme packs)
└── Upgrade to Pro (pin favorites; extra themes)

markdown
Copy code

**Navigation Model:** Stack (Home → AddPerson/Settings/PersonDetail).
- HomeList remains stateful; overlays (Popup/Toast) are **in-place** on Home.

---

## 2) Primary Screen — HomeList

### Purpose
Daily surface: see people, send presence quickly via **Popup**. 

### Layout (dim mode)
- **Header:** “LuvPing” + warm underline
- **List:** uniform dark cards (rounded 14px) with:
  - Avatar (glow on active)
  - Name + mini status line (“Last ping: 3h ago 😊”)
  - Right-aligned streak flame “🔥 7d”
- **FAB:** bottom-right “+” for Add Person

┌──────────────────────────────────────────────┐
│ LuvPing                                     │
│ ──────────────── (warm underline glow) ───── │
├──────────────────────────────────────────────┤
│ [ Card ]  💫 Ava                🔥 7d        │
│           Last ping: 3h ago 😊               │
│                                              │
│ [ Card ]  💫 Jordan             🔥 2d        │
│           Last ping: today                  │
│                                              │
│ [ Card ]  💫 Taylor             —            │
│           Last ping: yesterday 😐           │
│                                              │
│                              ⊕  (FAB Add)   │
└──────────────────────────────────────────────┘

yaml
Copy code

### Interactions (from Interaction SoT v0)
- **Tap row** → open **QuickActionPopup** near thumb (auto place above/below; blur backdrop; lock scroll)
  - Actions: `🤍 | 😊 😐 😞 | 💬`
  - On select: medium haptic → auto close → bottom toast “Sent … — Undo? (5s)”
- **Tap outside** → dismiss popup (no action)

### Empty States
- No connections yet → centered card with “Add someone you care about” + FAB hint.

---

## 3) QuickActionPopup (overlay component)

### Purpose
Unified, intentional send surface; prevents accidental taps.

### Placement Logic
- If tap Y in top half → show below; else above.
- Nudge 8–12px from screen edges to avoid clipping.

### Visual
Frosted bubble (radius 20px) with large icons (54px). Blur backdrop (opacity ≈ 0.15).  
Scroll locked. Tap outside to dismiss.

### Haptics & Motion
- Open: light impact; scale-in spring (0.8→1.0, 220ms)
- Select: medium impact; ripple pulse (250ms)
- Dismiss: 180ms fade

---

## 4) AddPerson

### Purpose
Create a connection via **code/link/QR**; both users must confirm.

### Flow (MVP)
1) Tap FAB → AddPerson
2) Two tabs:
   - **Invite**: generates 6-digit code & share link; shows “Waiting for confirm…”
   - **Join**: enter code or scan QR
3) **Confirm modal**: “Connect with Ava?” → Accept / Decline
4) On success → HomeList updates immediately (optimistic)

*Note:* We’ll decide per-person reminders later. For now, a global reminder lives in Settings.

---

## 5) Settings

### Purpose
Housekeeping + upgrade.

### Sections
- **Account**: Apple/Google sign-in, Sign out, **Delete Account**
- **Reminders**: set global time (default 9:00 PM local)
- **Appearance**: Dark & Cozy preset (base), Theme packs list (Pro = more)
- **Upgrade to Pro**: Pin favorites (top 5), extra themes, future custom sounds/emojis

---

## 6) PersonDetail (Later Phase)

### Purpose
Optional deeper view; not required for MVP.

### Content
- **Streak card**: Current streak + Best streak
- **Vibe history**: simple weekly emoji chart
- **Connection options**: Pin (Pro), per-person reminder (later)

---

## 7) Navigation & State Rules

- **HomeList retains scroll & state** when navigating away and back.
- **Popup + Toast live inside HomeList** (not global nav routes) to ensure finger-aware positioning.
- **System back**:
  - If Popup open → closes Popup
  - Else if Toast visible → no-op (don’t pop)
  - Else → navigate back normally

---

## 8) Folder Plan (client app)

When we scaffold the app (Expo), use this structure:

apps/mobile/
app/ # (if using Expo Router) or screens/ with React Navigation
src/
components/
QuickActionPopup/
QuickActionPopup.tsx
styles.ts
Toast/
Toast.tsx
Header/
Header.tsx
PersonCard/
PersonCard.tsx
screens/
HomeList.tsx
AddPerson.tsx
Settings.tsx
PersonDetail.tsx # later
hooks/
useHaptics.ts
useQuickActions.ts
useToast.ts
lib/
theme.ts # Dark & Cozy tokens (shared later to packages/ui)
placements.ts # computePopupPosition(tapY, windowH, rowLayout)
state/
uiSlice.ts # popup open/close, toast queue, selection
styles/
globals.ts

yaml
Copy code

> In a monorepo, we’ll later lift `theme.ts` to `packages/ui` and lint configs to `packages/config`.

---

## 9) UI Contracts (component inputs/outputs)

**`<PersonCard />`**
- Props: `{ id, name, avatarUrl, lastPingAt, lastMood, streak, onPress }`
- Emits: `onPress(rowLayout, tapPosition)` → HomeList decides where to place Popup

**`<QuickActionPopup />`**
- Props: `{ anchor: {x,y}, preferred: 'above'|'below', onSelect(type, mood), onDismiss }`
- Types: `type = 'hug'|'mood'|'checkin'`; `mood = 'happy'|'meh'|'sad'|null`

**`<Toast />`**
- Props: `{ text, actionLabel?: 'Undo', onAction?, durationMs = 5000 }`

---

## 10) Event Log (for analytics/debug)

- `ui.popup_open` { personId, anchor, placement }
- `ui.popup_dismiss` { reason: 'outsideTap'|'select' }
- `ping.send` { personId, type, mood? }
- `toast.show` { variant: 'undo_ping' }
- `toast.action` { action: 'undo' }

---

## 11) Acceptance Criteria (for this SoT)

- HomeList is the **single daily hub**
- Popup always opens near thumb and never clips
- Scroll locks during Popup; tap outside cancels
- Toast with Undo appears bottom center for 5s
- Settings contains Reminder & Appearance, and Upgrade entry
- AddPerson supports invite + join flows with confirmation

---

## 12) Open Questions (tracked)
- Per-person reminders (vs global) — later decision
- PersonDetail depth (ship later)
- Theme pack grouping for Pro (naming, previews)

---

## 13) Out of Scope (v0)
- Chat, media, or long messages
- Public profiles / discovery
- Complex analytics (only simple counts later)