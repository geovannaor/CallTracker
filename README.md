# Call Tracker

**A pocket-sized shift tracker for support agents who want to know what their day actually looked like.**

## Why I built this

I work inbound technical support for commercial customers. Dozens of calls a shift, back to back, and by 4:30 most of them blur together. I wanted a way to see the shape of my day: what kinds of calls I took, how they ended, and when the waves hit.

The first version lived inside an AI chat as an interactive widget. It worked, until I noticed my numbers didn't agree with each other: **2 calls logged, 2 service visits, but only 1 call type recorded.** The counters were being tracked separately, so they could drift out of sync.

That bug changed how I rebuilt it. Instead of keeping running totals, the app now stores one record per call and calculates everything else from those records. Totals can't disagree with each other when they all come from the same source.

Then I thought of my coworkers, so it grew up: adjustable shifts and breaks, a first-time setup screen, and a shared call type guide so everyone classifies calls the same way.

---

## What it does

| Screen | What you get |
|---|---|
| **Today** | Log a call in two taps (type, then outcome). Live counters, today's breakdown, and an hour-by-hour shift pace chart |
| **History** | Every shift you've logged, newest first, with an outcome mix bar for each day |
| **Day detail** | Tap any day for its full breakdown, pace chart, and a timestamped call log |
| **Settings** | Set your shift and breaks. Pace windows rebuild automatically, with a live preview before you save |

---

## The call types

Every call gets one type. These definitions are the whole system. Without them, one person's "Detective case" is another person's "Boss battle," and nobody's numbers mean the same thing.

| Type | Meaning |
|---|---|
| ✓ **Easy money** | Straightforward, predictable issue |
| ? **Detective case** | You actually have to troubleshoot |
| ! **Wild card** | "How did we even get here?" |
| ♡ **Therapist call** | 40% technical support, 60% emotional support |
| ↻ **Please reboot it** | Self-explanatory |
| ⚔︎ **Boss battle** | Complicated issue or customer, and you're fighting for your life |
| ★ **Bless you** | Pleasant customer who restores your faith in humanity |

And every call ends one of four ways: **Resolved**, **Service Visit**, **Transfer**, or **Escalation**.

---

## How it works

### One record per call

Each call is stored as a single event:

```json
{
  "id": "c_1727787720000_k3x9p",
  "ts": "2026-10-01T12:42:00.000Z",
  "type": "boss",
  "outcome": "service_visit"
}
```

Nothing else is counted. Every number on screen (totals, breakdowns, pace, history) is derived from this list. Undo removes one record. Reset removes a day's records. There's no second set of numbers to fall out of sync.

If that pattern looks familiar, it should. It's the same principle behind log management in a security operations center: store the raw events, derive the views. You can always build a new dashboard from good logs. You can't rebuild logs from a dashboard.

### Pace windows are calculated, not hardcoded

Shifts and breaks differ across the team, so the app builds its pace chart from two inputs:

1. Slice the shift at every clock hour.
2. Cut each break out of whatever window it lands in.
3. Whatever's left becomes a pace window.

An 8:00 to 4:30 shift with breaks at 10:00 (15 min), 12:00 (30 min), and 2:00 (15 min) produces:

`8–9` · `9–10` · `10:15–11` · `11–12` · `12:30–1` · `1–2` · `2:15–3` · `3–4` · `4–4:30`

A call logged during a break counts toward the window that just ended, because that's usually when you're wrapping up notes.

### History remembers the schedule it was logged under

When you log your first call of the day, the app saves a snapshot of your schedule with that date. If you change your shift next month, last month's pace charts don't get redrawn with the wrong windows. Record what was true at the time, not what's true now.

### Validation before save

Settings won't save a schedule that doesn't make sense:

| Rule | Message |
|---|---|
| End after start | "Shift end must be after shift start." |
| Breaks inside the shift | "Each break must fall within your shift." |
| No overlapping breaks | "Two breaks overlap. Adjust one of them." |

## Privacy by design

| Question | Answer |
|---|---|
| Where does my data go? | Nowhere. It lives in your phone's browser storage and never leaves the device |
| Is there an account or server? | No. No login, no backend, no tracking |
| Can coworkers see my calls? | No. Each person's data stays on their own phone |
| What gets stored? | Timestamps and categories only. No customer names, account numbers, or call notes, by design |

The code is public. The data is private. That separation was a deliberate choice, not an accident.

**The trade-off:** because there's no server, your history lives on one device. Clearing your browser's website data will erase it.

---

## Using it

1. Open the live link on your phone.
2. **Add it to your Home Screen.** In Safari: Share → Add to Home Screen. This matters: Safari can clear storage for sites you haven't visited in a while, but Home Screen apps are exempt.
3. Set your shift and breaks on the setup screen, skim the call type guide, and save.
4. Log calls as you go: tap the type, tap the outcome. Done.


## What this project taught me

The interesting part of this build wasn't the interface. It was the decisions underneath it:

- **Store events, derive views.** A drift bug in a tiny tracker teaches the same lesson as a SIEM.
- **Definitions are infrastructure.** A shared taxonomy is what turns individual tallies into comparable data.
- **Record what was true at the time.** Snapshots protect history from future changes.
- **Plan for your own future.** Versioned data meant an upgrade without losing anyone's history.

Small tool. Real systems thinking.

---

Built by **Geovanna Orlando** · [Soft Armor HQ](https://softarmorhq.com)
*Technical mastery, with a human touch.*
