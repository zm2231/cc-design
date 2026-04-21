---
name: cc-design:init
description: Build UI with craft and consistency. Works for any interface — dashboards, apps, tools, landing pages, marketing sites.
---

## Required Reading — Do This First

Before writing any code, read:

1. `../skills/cc-design/SKILL.md` — foundation, principles, craft knowledge, and checks

Do not skip this.

---

## First Action

State immediately what you're doing before anything else:

```
Building: [brief description of what's being built]
```

No mode announcements, no narration. Just that one line, then work.

---

## Intent First — Answer Before Building

Before touching code, answer these out loud. The questions shift by context:

**For product UI** (dashboards, apps, tools, admin panels):

> **Who is this human?** Not "users." Where are they? What's on their mind? A teacher at 7am is not a developer at midnight.
> **What must they accomplish?** The verb. Grade submissions. Find the broken deployment. Approve the payment.
> **What should this feel like?** Warm like a notebook? Cold like a terminal? Dense like a trading floor?

**For marketing / landing pages:**

> **Who is this human?** What brought them here? What do they doubt?
> **What must they leave believing?** The conviction — not "understand the product" but "this is the one" or "I can trust these people."
> **What should this feel like?** Confident? Quiet? Kinetic? What visual register earns that belief?

If you cannot answer with specifics, stop and ask. Do not guess. Do not default.

---

## Check for Existing State

1. Read `.cc-design/system.md` if it exists — apply established patterns
2. Read `.cc-design/stitch.json` if it exists — note the locked design system ID for any Stitch work in this session

---

## Before Writing Each Component

State the intent AND the technical approach:

```
Intent: [who, what they need, how it should feel]
Palette: [foundation + accent — WHY these fit the product's world]
Depth: [borders / subtle shadows / layered — WHY]
Surfaces: [elevation scale — WHY this temperature]
Typography: [typeface choice — WHY it fits the intent]
Spacing: [base unit]
```

Every choice must be explainable. "It's common" or "it works" is not a choice — it's a default.

**The test:** If another AI given a similar prompt would produce the same output, you failed. The interface must emerge from THIS user, THIS problem, THIS intent.

---

## Suggest + Ask

Lead with exploration, then confirm:

```
Domain: [concepts from this product's world]
Color world: [colors that exist in this domain]
Signature: [one element unique to this product]

Direction: [approach that connects to the above]

[AskUserQuestion: "Does that direction feel right?"]
```

---

## Stitch

If Stitch MCP is connected, you can generate and iterate screens at any point — not just during extraction. Useful for:

- Visualizing a direction before writing code
- Generating variants quickly
- Testing how a landing page reads before building it

If `.cc-design/stitch.json` exists, lock the design system for all Stitch calls:
> "Use existing design system `assets/[designSystemId]`. Do not create a new one."

To generate a new screen without code: call `generate_screen_from_text` with a description of the page/component and the existing design system ID if locked.

---

## Seeding the System

If no system.md exists and the project has existing UI (or a live URL), offer:

```
No design system found. Want me to extract one first?

  1. Extract from this codebase → /cc-design:extract
  2. Extract from a live URL   → /cc-design:extract <url>
  3. Extract from Stitch       → /cc-design:extract --stitch <project-id>
  4. Start fresh               → I'll establish direction as we build
```

---

## After Every Task

Offer to save when you finish building UI:

"Want me to save these patterns to `.cc-design/system.md`?"

Always offer — new patterns should be captured whether system.md exists or not.
