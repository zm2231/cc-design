---
name: cc-design:audit
description: Check existing code against your design system for spacing, depth, color, and pattern violations.
---

# cc-design audit

Check existing code against your design system.

## Usage

```
/cc-design:audit <path>   # Audit specific file/directory
/cc-design:audit          # Audit common UI paths
```

## What to Check

**Governance:** system.md is the source of truth. Audit findings mean code should change, not system.md. Only update system.md when a pattern decision is being intentionally revised.

**If `.cc-design/system.md` exists:**

1. **Spacing violations**
   - Find spacing values not on defined grid
   - Example: 17px when base is 4px

2. **Depth violations**
   - Borders-only system → flag shadows
   - Subtle system → flag layered shadows
   - Allow ring shadows (0 0 0 1px)

3. **Color violations**
   - If palette defined → flag colors not in palette
   - Allow semantic grays

4. **Pattern drift**
   - Find buttons not matching Button pattern
   - Find cards not matching Card pattern

**Report format:**
```
Audit Results: src/components/

Violations:
  Button.tsx:12 - Height 38px (pattern: 36px)
  Card.tsx:8 - Shadow used (system: borders-only)
  Input.tsx:20 - Spacing 14px (grid: 4px, nearest: 12px or 16px)

Suggestions:
  - Update Button height to match pattern
  - Replace shadow with border
  - Adjust spacing to grid
```

**If no system.md:**

```
No design system to audit against.

Create a system first:
1. Build UI → establish system automatically
2. Run /cc-design:extract → create system from existing code
3. Run /cc-design:extract <url> → extract from a live page
```

## Implementation

1. Check for `.cc-design/system.md`
2. Parse system rules
3. Read target files (tsx, jsx, css, scss)
4. Compare against rules
5. Report violations with suggestions
