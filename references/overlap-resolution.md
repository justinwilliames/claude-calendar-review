# Overlap Resolution Playbook

When a protected block (lunch, decompress, morning, wind-down) clashes with another event, the resolution depends on two questions:

1. **Who owns the overlapping event?** (User vs someone else)
2. **Is the overlapping event movable without side effects?** (Focus block vs attendee meeting)

This file is the decision tree.

---

## The decision tree

```
Overlapping event detected with a protected block.
│
├── Is the overlapping event user-owned AND has no other attendees?
│   └── YES → Propose a MOVE of the overlapping event.
│             (e.g., focus block 12:30→13:30 becomes 13:00→14:00)
│             Apply on user approval.
│
├── Is the overlapping event user-owned BUT has attendees?
│   └── YES → FLAG for user. Options:
│             • Move the meeting (will notify attendees)
│             • Skip the protected block this instance
│             • Shift the protected block this instance only
│             NEVER auto-move without explicit instruction.
│
└── Is the overlapping event organised by someone else?
    └── YES → FLAG for user. Options:
              • Skip the protected block this instance (EXDATE on the series)
              • Shift the protected block this instance to a non-clashing slot
              • Manually ask the organiser to move
              NEVER move someone else's event.
```

---

## Worked examples

### Example 1 — Focus block overrunning lunch

- **Protected:** 🥪 Lunch Mon 12:00–13:00 (Banana)
- **Overlapping:** `Focus: Activation PRD` Mon 11:00–13:30 (Graphite, user-owned, no attendees)

**Resolution:** Propose moving the focus block to 11:00–12:00 + 13:00–14:00 (split) OR 13:00–15:30 (push) — whichever the user prefers. Apply on approval.

### Example 2 — Colleague's recurring meeting overlapping lunch

- **Protected:** 🥪 Lunch Tue 12:00–13:00 (Banana)
- **Overlapping:** `HOD Weekly meeting` Tue 12:30–13:00 (Blueberry, organised by Jacob, 8 attendees)

**Resolution:** Flag. Do NOT touch the HOD meeting. Offer the user:

1. **Skip Tuesday lunch** — add an EXDATE to the lunch series for Tuesday
2. **Shift Tuesday lunch** — change just the Tuesday instance to 13:00–14:00
3. **Ask Jacob to move HOD** — user does this manually outside the calendar

Present all three, let user pick.

### Example 3 — Focus block overlapping decompress

- **Protected:** 🌿 Decompress Wed 15:30–15:45 (Sage)
- **Overlapping:** `Focus: PRD review` Wed 14:00–16:00 (Graphite, user-owned)

**Resolution:** Propose shortening the focus block to 14:00–15:30 OR splitting to 14:00–15:30 + 15:45–16:00. Note that decompress is short enough that a split is usually preferable to shortening — the user wanted 2 hours of focus, not 90 minutes.

### Example 4 — Wind-down overlapping an external meeting

- **Protected:** 🌙 Wrap up & shut down Thu 16:30–17:00 (Flamingo)
- **Overlapping:** `Vendor demo` Thu 16:00–17:30 (Blueberry, external organiser)

**Resolution:** Flag. The wind-down is protected and the vendor meeting is not movable. Offer:

1. **Skip Thursday wind-down** — EXDATE on the series
2. **Shift Thursday wind-down** — to 17:30–18:00 just this instance
3. **Skip the vendor demo** — user makes that call separately

Note that "skip wind-down" is often the right answer for one-off external commitments; persistent overlaps with the wind-down slot are a signal to renegotiate the slot itself.

---

## When to apply EXDATEs vs single-instance overrides

If the user wants to skip a protected block on **one specific day**:

- **One-off, recent, won't repeat:** delete the single instance (Google Calendar handles this as an automatic EXDATE)
- **Recurring pattern they want to formalise** (e.g., "I never have lunch on Wednesdays"): add a permanent EXDATE or change the BYDAY rule on the series

**Default behaviour:** instance-only override. Do not change the master series unless the user explicitly says "always" or "never on X days".

---

## Edge cases

### Back-to-back events vs true overlap

- **Back-to-back** (e.g., lunch ends 13:00, meeting starts 13:00 exactly): not an overlap. Do NOT flag.
- **One-minute touch** (e.g., lunch ends 13:00, decompress starts 12:55): treat as overlap. Surface to user.

### Recurring overlaps

If the same recurring meeting overlaps a recurring protected block **every week**:

- Surface this as a structural issue, not a per-instance one.
- Recommend: rename the protected block's slot for that weekday, or accept a recurring EXDATE.

Don't generate five identical "flag this week's Tuesday lunch" alerts. Surface the pattern once.

### Multi-day events overlapping protected blocks

- All-day events (e.g., conferences, OOO): the protected blocks should automatically be skipped for those days. Add EXDATEs automatically when an all-day event is detected.
- Multi-hour events that span lunch (e.g., a 3-hour workshop 11:00–14:00): treat as a single overlap — flag, don't propose splitting the workshop.

### User's own back-to-back blocks

If the user has a focus block 09:30–11:30 and another focus block 11:30–13:00, treat them as one logical block for overlap purposes — moving one in isolation breaks the flow. Surface both together.

---

## What NOT to do

- **Don't propose moving a meeting "by 30 minutes" without saying where it goes.** Always propose specific new times.
- **Don't move events into slots that have other meetings.** Re-check the destination slot before proposing.
- **Don't move events across days** unless the user explicitly asks. "Move my Monday focus block to Tuesday" is a different operation from "resolve this overlap".
- **Don't apply more than one move in a single approval without listing every move first.** If three focus blocks overlap protected slots, show all three proposed moves before mutating any.
- **Don't silently send attendee notifications.** When mutating user-organised events with attendees, pass `notificationLevel: "NONE"` or warn the user that an attendee notification will fire.
