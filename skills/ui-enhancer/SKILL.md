---
name: ui-enhancer
description: 'Analyze iOS/SwiftUI views from screenshots + code to recommend and implement UI improvements with tests. Covers space efficiency, visual hierarchy, accessibility, information density, interaction patterns, HIG compliance, dark mode, performance, and design system conformance. Triggers: "enhance this UI", "ui enhancer", "improve this view", "screen review", "ux audit".'
version: 1.0.0
author: Terry Nyberg
license: MIT
allowed-tools: [Read, Grep, Glob, Bash, Write, Edit, AskUserQuestion]
metadata:
  tier: execution
  category: ux
---

# UI Enhancer

> **Quick Ref:** Screenshot + code analysis of any iOS/SwiftUI view. Brief interview to focus the audit, 9-domain analysis, ASCII before/after mockups, implementation playbook with exact code diffs, tests for new components, and progress tracking.

<ui-enhancer>

You are performing a systematic UI enhancement on a specific iOS/SwiftUI view, analyzing both the visual screenshot and the underlying code, then implementing improvements with tests.

**Required output:** Every finding MUST include a severity rating (Critical / High / Medium / Low) and estimated implementation effort (Trivial / Small / Medium / Large).

## Quick Commands

| Command | Description |
|---------|-------------|
| `/ui-enhancer` | Full audit with interview |
| `/ui-enhancer space` | Space efficiency analysis only |
| `/ui-enhancer hierarchy` | Visual hierarchy analysis only |
| `/ui-enhancer density` | Information density analysis only |
| `/ui-enhancer interaction` | Interaction patterns analysis only |
| `/ui-enhancer accessibility` | Accessibility audit only |
| `/ui-enhancer hig` | HIG compliance check only |
| `/ui-enhancer dark-mode` | Dark mode audit only |
| `/ui-enhancer performance` | Performance impact analysis only |
| `/ui-enhancer design-system` | Design system compliance only |
| `/ui-enhancer compare` | Compare before/after screenshots for progress |
| `/ui-enhancer revert` | Undo all changes back to last checkpoint |
| `/ui-enhancer batch [path]` | Audit all views in a directory, rank by severity |
| `/ui-enhancer --capture` | Capture screenshot from running simulator (optional) |
| `/ui-enhancer --devices` | Analyze layout across device sizes (optional) |

---

## Phase 1: Interview

Before analyzing, run a brief intake to focus the audit on what matters most.

```
questions:
[
  {
    "question": "What's the main reason for this review?",
    "header": "Focus",
    "options": [
      {"label": "General polish", "description": "No specific complaint \u2014 just want it to be better"},
      {"label": "Specific problem", "description": "Users or I have noticed something wrong"},
      {"label": "Pre-release review", "description": "Final check before shipping"},
      {"label": "Competitive parity", "description": "Want to match or exceed a competitor's UX"}
    ],
    "multiSelect": false
  },
  {
    "question": "Which aspects matter most right now?",
    "header": "Priority",
    "options": [
      {"label": "All domains (Recommended)", "description": "Full 9-domain analysis"},
      {"label": "Visual \u2014 layout and hierarchy", "description": "Space, visual weight, information density"},
      {"label": "Interaction \u2014 usability", "description": "Touch targets, discoverability, feedback"},
      {"label": "Technical \u2014 performance and compliance", "description": "Dark mode, perf, HIG, design system"}
    ],
    "multiSelect": false
  }
]
```

**If "Specific problem"**, follow up:

```
questions:
[
  {
    "question": "What's the specific issue?",
    "header": "Issue",
    "options": [
      {"label": "Too much wasted space", "description": "Content doesn't start until halfway down the screen"},
      {"label": "Hard to find things", "description": "Users can't discover features or actions"},
      {"label": "Looks cluttered or confusing", "description": "Too much competing for attention"},
      {"label": "I'll describe it", "description": "Let me explain the problem"}
    ],
    "multiSelect": false
  }
]
```

**If "Competitive parity"**, ask for a competitor screenshot to enable Domain 10 (Competitive Comparison).

The interview determines:
- Which domains to run (all 9, or a focused subset)
- Severity weighting (user-reported problems get Critical minimum)
- Whether to include competitive comparison

---

## Phase 2: Gather Input

```
questions:
[
  {
    "question": "How would you like to provide the view to analyze?",
    "header": "Input",
    "options": [
      {"label": "Screenshot + file path (Recommended)", "description": "Deepest analysis \u2014 visual + structural"},
      {"label": "Screenshot only", "description": "Visual analysis, recommendations without code changes"},
      {"label": "File path only", "description": "Code analysis, structural improvements"},
      {"label": "Capture from simulator", "description": "I'll capture the screenshot automatically (optional)"}
    ],
    "multiSelect": false
  }
]
```

**If "Capture from simulator"** (optional feature):
```bash
xcrun simctl io booted screenshot /tmp/ui-enhancer-capture.png
```
Then read the captured screenshot.

---

## Phase 3: Screenshot Analysis

When a screenshot is provided, analyze it visually:

### 3a. First Impression (3-Second Test)
- What does the user see first? Is it what matters most?
- Can the user tell what this screen does within 3 seconds?
- Is the primary action obvious?

### 3b. Visual Scan Pattern
- Does the layout follow natural eye flow (F-pattern or Z-pattern)?
- Are related elements grouped visually?
- Is there a clear visual hierarchy (primary > secondary > tertiary)?

### 3c. Content-to-Chrome Ratio
- Measure: What percentage of the viewport is actual content vs. navigation chrome?
- **Target:** >60% content on iPhone, >70% on iPad
- Flag any view where content ratio falls below 50%

---

## Phase 4: Code Analysis

When a SwiftUI file is provided, read it and analyze:

### 4a. View Structure
- Total nesting depth (VStack chains)
- Number of distinct visual sections
- Spacing and padding accumulation
- Bottom padding for floating elements

### 4b. Component Audit
- Redundant UI elements (same info shown twice)
- Elements that could be merged (separate rows that should be one)
- Hidden/conditional elements wasting space when collapsed
- Action button sizing and placement

### 4c. Text and Typography
- Font hierarchy clarity (is there a clear primary > secondary > tertiary?)
- Line limit truncation (important text cut off while less important text has room)
- Dynamic Type support (`.system(size:)` vs semantic fonts)

### 4d. Color Usage
- Meaningful vs. decorative color
- Competing colors
- Contrast sufficiency
- Information redundancy (color + icon + text)

---

## Phase 5: Domain Analysis

Run the applicable domains based on the interview:

### Domain 1: Space Efficiency

**Goal:** Maximize content-to-chrome ratio; minimize wasted vertical space.

| Check | What to Look For | Common Fix |
|-------|-----------------|------------|
| Header overhead | Custom headers stacking on nav bar | Collapse or merge headers |
| Dual button rows | Action buttons on separate line from navigation | Merge into one row |
| Section headers | Oversized section titles with icons | Reduce font size, remove decorative icons |
| Bottom padding | Excessive padding for floating elements | Reduce to actual element height + margin |
| Hints/tips | Permanent hints that should dismiss after first use | Use coach marks or remove after learning |
| Photo sections | Separate photo rows when thumbnail could be inline | Merge photo into title/header row |
| Dividers/spacers | Excessive VStack spacing or explicit Spacer() | Reduce spacing values |

**Metrics:**
- Content starts at: [Y position in points from top]
- First interactive element at: [Y position]
- Content-to-chrome ratio: [percentage]
- Target: Content should start within 120pt of safe area top on iPhone

---

### Domain 2: Visual Hierarchy

**Goal:** The most important information should be the most prominent.

| Check | What to Look For | Common Fix |
|-------|-----------------|------------|
| Title prominence | Is the item/screen title the dominant element? | Ensure title is largest text |
| Competing elements | Multiple elements fighting for attention | Differentiate with size/weight/color |
| Status overload | Status indicators louder than content | Reduce to subtle badges |
| Date/number sizing | Dates or numbers displayed too large | Use caption/footnote for secondary data |
| Action vs. content | Action buttons more prominent than content | Tone down button styling |
| Truncation | Important text truncated while less important text has room | Allow wrapping or reprioritize |
| Color dominance | Bright colors on secondary elements | Reserve bright colors for primary actions |

**Analysis technique:**
1. Squint at the screenshot — what stands out?
2. That should be the primary content, not navigation chrome
3. If navigation or status draws the eye first, hierarchy is wrong

---

### Domain 3: Information Density

**Goal:** Show the right amount of information — not too sparse, not too cluttered.

| Check | What to Look For | Common Fix |
|-------|-----------------|------------|
| Sparse rows | List rows with too much whitespace | Reduce padding, show more per row |
| Dense rows | Too much crammed into one row | Progressive disclosure |
| Redundant info | Same data shown in multiple places | Remove duplicates |
| Hidden useful info | Important data behind taps/scrolling | Surface in primary view |
| Badge overload | Too many status badges on one element | Prioritize, hide secondary |
| Empty states | Large empty areas when data is missing | Collapse section |
| Nonsense values | Negative numbers, meaningless dates | Use human labels or hide |

**Density targets:**
- List row: 2-3 lines max (title + subtitle + trailing status)
- Card: 4-6 data points visible without scrolling
- Form section: 3-5 fields visible without scrolling

---

### Domain 4: Interaction Patterns

**Goal:** Every interactive element should be discoverable, predictable, and satisfying.

| Check | What to Look For | Common Fix |
|-------|-----------------|------------|
| Touch targets | Elements smaller than 44x44pt | Increase frame/padding |
| Ambiguous buttons | Buttons that look like labels | Add clear button styling |
| Combined buttons | Two features sharing one button | Separate into distinct buttons |
| Gesture-only actions | Actions only via swipe/long-press | Add visible button alternative |
| Dead ends | Screens with no clear next action | Add CTA or navigation hint |
| Feedback gaps | Actions with no visual/haptic response | Add animation or haptic |
| Scroll discovery | Content below fold with no indicator | Add hint or gradient |
| Menu depth | Important actions buried in menus | Surface frequently-used actions |

---

### Domain 5: Accessibility

**Goal:** Every user, regardless of ability, can use the view effectively.

| Check | What to Look For | Common Fix |
|-------|-----------------|------------|
| Color-only info | Information conveyed only by color | Add icon + text (triple redundancy) |
| Fixed fonts | `.system(size:)` instead of semantic | Use Dynamic Type |
| Missing labels | Images/icons without accessibility labels | Add `.accessibilityLabel()` |
| Small text | Text below 11pt that doesn't scale | Use `.caption` minimum |
| Contrast ratio | Low contrast text on backgrounds | Ensure 4.5:1 (WCAG AA) |
| VoiceOver order | Reading order doesn't match visual | Reorder or group |
| Motion | Animations without Reduce Motion check | Check accessibility setting |

---

### Domain 6: HIG Compliance

**Goal:** Follow Apple Human Interface Guidelines for platform consistency.

| Check | What to Look For | Common Fix |
|-------|-----------------|------------|
| Navigation | Custom back buttons, non-standard patterns | Use system NavigationStack |
| Tab bar | Incorrect icons or labels | Follow SF Symbol + short label |
| Sheet presentation | Missing drag indicator or dismiss | Add `.presentationDragIndicator(.visible)` |
| System colors | Hard-coded colors | Use `.primary`, `.secondary` |
| Platform differences | iOS-only patterns on macOS | Use `#if os(iOS)` |
| Safe areas | Content under notch or home indicator | Respect safe area insets |
| Standard controls | Custom controls duplicating system | Use SwiftUI standard controls |

---

### Domain 7: Dark Mode

**Goal:** The view should look correct and intentional in both light and dark mode.

| Check | What to Look For | Common Fix |
|-------|-----------------|------------|
| Hardcoded colors | `Color.white`, `Color.black`, hex values | Use semantic colors (`.primary`, `.background`) |
| Background assumptions | `.background(Color.white)` | Use `.background(Color(.systemBackground))` |
| Shadow visibility | Shadows invisible in dark mode | Use `.shadow` with adaptive opacity |
| Image contrast | Images with white/transparent backgrounds | Add dark mode variants or tinted backgrounds |
| Separator visibility | Light separators disappearing | Use `.separator` system color |
| Accent consistency | Accent colors that clash in dark mode | Test all accent colors in both modes |
| Material usage | Solid backgrounds where materials work better | Use `.ultraThinMaterial` for overlays |

**Analysis:** If screenshot provided, check if the view uses light or dark mode. If code available, grep for hardcoded colors.

---

### Domain 8: Performance Impact

**Goal:** UI patterns should not cause frame drops, excessive redraws, or memory issues.

| Check | What to Look For | Common Fix |
|-------|-----------------|------------|
| Heavy body | Complex expressions in view body | Extract to computed properties |
| Inline images | Image decoding in body | Use `AsyncCachedImage` or background decoding |
| Missing lazy | Large lists without `LazyVStack` | Switch to lazy containers |
| Excessive state | Too many `@State` vars causing redraws | Consolidate or use `@Observable` |
| Geometry readers | GeometryReader in scroll views | Use `.onGeometryChange` or remove |
| Conditional complexity | Deep if/else chains in body | Extract to `@ViewBuilder` functions |
| Animation cost | Heavy animations on low-end devices | Reduce or check Reduce Motion |

**Analysis:** Read the SwiftUI file and check for known performance anti-patterns. Flag files over 500 lines that could benefit from extraction.

---

### Domain 9: Design System Compliance

**Goal:** The view should follow the project's established design system.

**How it works:**
1. Read `CLAUDE.md` for project-level design rules
2. Read any design system files (`DESIGN_SYSTEM.md`, `StuffolioStyleGuide.swift`, etc.)
3. Compare the view against documented patterns

| Check | What to Look For | Common Fix |
|-------|-----------------|------------|
| Color palette | Colors outside the approved palette | Replace with design system colors |
| Spacing values | Non-standard spacing/padding | Use `Spacing.*` constants |
| Component usage | Custom components where standard ones exist | Replace with `SheetHeader`, `SemanticIconCircle`, etc. |
| Card styles | Cards not using `.stuffolioCard()` or `.actionCard()` | Apply standard modifiers |
| Icon style | Inconsistent icon rendering or sizing | Use `@ScaledMetric` and standard patterns |
| Section structure | Sections not using `CollapsibleSection` | Adopt standard section component |
| Sheet pattern | Sheets not using `SheetContainer` + `SheetHeader` | Apply standard sheet pattern |

**Note:** This domain is project-specific. If no design system docs are found, skip this domain and note that establishing a design system would benefit consistency.

---

### Domain 10: Competitive Comparison (On Request)

Only runs when user provides a competitor screenshot during interview.

| Analysis | What to Compare |
|----------|----------------|
| Information density | How much data is visible without scrolling? |
| Visual hierarchy | What does each app emphasize first? |
| Interaction patterns | How many taps to accomplish the same task? |
| Space efficiency | Content-to-chrome ratio comparison |
| Unique strengths | What does each app do better? |

Output as a side-by-side comparison table.

---

## Phase 6: Generate Report

### Report Structure

```
## UI Enhancer Report: [ViewName]

### Focus: [from interview — e.g., "General polish" or "Specific: too much wasted space"]

### Screenshot Analysis
- Content-to-chrome ratio: X%
- First content element at: Ypt from top
- Primary action visibility: [Obvious / Hidden / Ambiguous]
- 3-second test: [Pass / Fail — what draws the eye]

### Findings

| # | Domain | Finding | Severity | Effort | Recommendation |
|---|--------|---------|----------|--------|---------------|
| 1 | Space | 45% of screen is header chrome | Critical | Medium | Merge photo into title row |
| 2 | Hierarchy | Date text dominates over item title | High | Small | Replace with status circle |
| 3 | Dark Mode | 3 hardcoded Color.white references | Medium | Trivial | Replace with semantic colors |
| ... | ... | ... | ... | ... | ... |

### UX Score

| Domain | Score | Notes |
|--------|-------|-------|
| Space Efficiency | 4/10 | Content starts at 410pt |
| Visual Hierarchy | 6/10 | Date text too dominant |
| Information Density | 7/10 | Good density, some redundancy |
| Interaction | 5/10 | Combined buttons, gesture-only deletes |
| Accessibility | 8/10 | Good labels, some fixed fonts |
| HIG Compliance | 7/10 | Minor deviations |
| Dark Mode | 6/10 | Some hardcoded colors |
| Performance | 8/10 | Clean structure |
| Design System | 7/10 | Mostly compliant |
| **Overall** | **6.4/10** | |

### Before/After ASCII Mockup

BEFORE:
┌─────────────────────────┐
│ [Nav Bar]               │
│ [Photo Row - 56pt]      │
│ [Title Row - 64pt]      │
│ [Tab Chips - 40pt]      │
│ [Action Buttons - 32pt] │
│ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ │
│ [Content starts here]   │
│ ...                     │
└─────────────────────────┘
Content ratio: 39%

AFTER:
┌─────────────────────────┐
│ [Nav Bar]               │
│ [Photo+Title - 64pt]    │
│ [Chips + Buttons - 40pt]│
│ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ │
│ [Content starts here]   │
│ ...                     │
│ ...                     │
│ ...                     │
└─────────────────────────┘
Content ratio: 63%

### Implementation Priority

**Quick Wins (do first):**
1. [Finding] — [exact code change]

**Medium Effort:**
2. [Finding] — [approach]

**Nice-to-Have:**
3. [Finding] — [approach]
```

---

## Phase 7: Implementation Playbook

For each finding, generate the exact code change — not just a description.

### Playbook Entry Format

```
### Fix #1: [Finding title]

**File:** `Sources/Views/Detail/EnhancedItemDetailView.swift`
**Lines:** 180-265

**Before:**
[exact code block to replace]

**After:**
[exact replacement code]

**Why:** [one sentence explaining the UX improvement]

**Test:** [what to verify after applying]
```

Offer to apply changes:

```
questions:
[
  {
    "question": "How would you like to apply these changes?",
    "header": "Implement",
    "options": [
      {"label": "Apply all (Recommended)", "description": "Implement all fixes with keep/revert after each"},
      {"label": "Quick wins only", "description": "Only Trivial/Small effort changes"},
      {"label": "One at a time", "description": "Apply and verify each fix individually"},
      {"label": "Save playbook", "description": "Save to file, implement later"}
    ],
    "multiSelect": false
  }
]
```

---

## Phase 7b: Revert Safety

### Git Checkpoint (Automatic)

Before applying ANY code changes, silently create a git checkpoint:

```bash
# Check for uncommitted changes first
git status --porcelain

# If clean, create a named checkpoint commit
git commit --allow-empty -m "ui-enhancer: checkpoint before changes"

# If dirty, stash changes first
git stash push -m "ui-enhancer: pre-enhancement stash"
git commit --allow-empty -m "ui-enhancer: checkpoint before changes"
git stash pop
```

Store the checkpoint commit hash for revert:
```bash
UI_ENHANCER_CHECKPOINT=$(git rev-parse HEAD)
```

**Important:** The checkpoint is created silently — don't ask the user. Just do it before the first edit.

### Incremental Apply with Keep/Revert

After applying each fix, ask:

```
questions:
[
  {
    "question": "Fix #N applied: [description]. Keep this change?",
    "header": "Review",
    "options": [
      {"label": "Keep", "description": "This looks good, move to next fix"},
      {"label": "Revert this fix", "description": "Undo this specific change, continue with others"},
      {"label": "Revert all", "description": "Undo everything back to checkpoint"},
      {"label": "Stop here", "description": "Keep changes so far, skip remaining fixes"}
    ],
    "multiSelect": false
  }
]
```

**If "Revert this fix":**
```bash
# Undo the last edit(s) for this specific fix
git checkout -- [files modified by this fix]
```
Then continue to the next fix.

**If "Revert all":**
```bash
# Reset to checkpoint
git reset --hard $UI_ENHANCER_CHECKPOINT
```
Report: "All changes reverted to checkpoint. Your code is back to where it was."

**If "Stop here":**
Skip remaining fixes. Offer to save the remaining playbook entries to a file for later.

### Batch Revert Command

Available anytime after an enhancement session:

```
/ui-enhancer revert
```

This command:
1. Finds the most recent `ui-enhancer: checkpoint` commit
2. Shows what would be reverted (files changed since checkpoint)
3. Asks for confirmation:

```
questions:
[
  {
    "question": "Revert to checkpoint? This will undo all UI Enhancer changes since [timestamp].",
    "header": "Revert",
    "options": [
      {"label": "Revert all", "description": "Reset to checkpoint \u2014 undo all UI Enhancer changes"},
      {"label": "Show diff first", "description": "Show what would be reverted before deciding"},
      {"label": "Cancel", "description": "Keep current changes"}
    ],
    "multiSelect": false
  }
]
```

**If "Revert all":**
```bash
# Find checkpoint
CHECKPOINT=$(git log --oneline --all | grep "ui-enhancer: checkpoint" | head -1 | awk '{print $1}')

# Reset
git reset --hard $CHECKPOINT

# Clean up the checkpoint commit
git reset --soft HEAD~1
```

**If "Show diff first":**
```bash
git diff $CHECKPOINT..HEAD --stat
```
Then re-prompt for revert decision.

### Safety Rules

1. **Never force-push** — revert only affects local commits
2. **Never revert past user commits** — only revert to the ui-enhancer checkpoint
3. **Warn if checkpoint is old** — if checkpoint is more than 1 hour old, warn that other changes may have been made since
4. **No revert if already pushed** — if changes were pushed to remote, warn and suggest `git revert` instead of `reset --hard`

---

## Phase 8: Tests

After implementing changes, generate tests for any new or modified components.

### What Gets Tests

| Change Type | Test Type | Example |
|-------------|-----------|---------|
| New component extracted | Unit test (Swift Testing) | `WarrantyStatusCircle` properties and states |
| Layout change | Preview verification | `#Preview` with all states |
| Accessibility change | Accessibility audit test | VoiceOver label coverage |
| Interaction change | UI test (if warranted) | Button tap triggers expected action |
| Performance change | No test needed | Verified by profiling |

### Test Template

```swift
import Testing
@testable import [AppModule]

@Suite("[ComponentName] Tests")
struct [ComponentName]Tests {
    @Test("Renders correct state for [condition]")
    func [testName]() {
        // Arrange
        // Act
        // Assert with #expect
    }
}
```

### When to Skip Tests
- Pure styling changes (color, font, padding) — verify visually
- Layout reordering — verify via preview/screenshot
- Removing unused code — no behavior to test

---

## Phase 9: Progress Tracking

After implementation, offer to re-run the audit to measure improvement.

```
questions:
[
  {
    "question": "Changes applied. Want to measure the improvement?",
    "header": "Progress",
    "options": [
      {"label": "Re-audit now", "description": "Run the audit again and compare scores"},
      {"label": "Capture new screenshot", "description": "Take a fresh screenshot and compare side-by-side"},
      {"label": "Done for now", "description": "Save the report and move on"}
    ],
    "multiSelect": false
  }
]
```

### Progress Report Format

```
## Progress: [ViewName]

| Domain | Before | After | Change |
|--------|--------|-------|--------|
| Space Efficiency | 4/10 | 7/10 | +3 |
| Visual Hierarchy | 6/10 | 8/10 | +2 |
| Overall | 6.4/10 | 8.1/10 | +1.7 |

Content-to-chrome ratio: 39% → 63% (+24%)
Findings resolved: 8/12
Tests added: 5
```

---

## Optional Features

These features are available but not run by default. User must explicitly request them.

### Device Size Simulation (Optional)

Analyze how the view behaves across device sizes by examining spacing, padding, and layout constraints in code.

```
/ui-enhancer --devices
```

| Device | Width | Check |
|--------|-------|-------|
| iPhone SE | 375pt | Does content fit? Truncation? |
| iPhone 16 | 393pt | Standard layout |
| iPhone 16 Pro Max | 430pt | Extra space utilized? |
| iPad 11" | 820pt | Split view behavior? |

### Simulator Screenshot Capture (Optional)

Automatically capture a screenshot from a running simulator.

```
/ui-enhancer --capture
```

```bash
# List available simulators
xcrun simctl list devices booted

# Capture screenshot
xcrun simctl io booted screenshot /tmp/ui-enhancer-capture.png
```

---

## Analysis Heuristics Reference

### Content-to-Chrome Ratio

```
Chrome = status bar + nav bar + tab bar + custom headers + toolbars + search + pickers
Content = everything else

Targets:   iPhone >60% | iPad >70% | macOS >75%
Flags:     <50% Critical | 50-60% High | 60-70% Medium | >70% Good
```

### Vertical Space Budget (iPhone)

```
Viewport: ~667pt (SE) to ~852pt (Pro Max)
Status bar: 54pt (Dynamic Island) / 44pt (notch) / 20pt (none)
Nav bar: 44pt | Tab bar: 49pt + 34pt safe area

Budget: Header max 80pt | Actions max 44pt (one row) | Content >60% of remaining
```

### Visual Hierarchy Scoring

```
For each element, assign visual weight:
  Size: Large=3, Medium=2, Small=1
  Weight: Bold=2, Regular=1, Light=0
  Color: Bright=2, Standard=1, Muted=0
  Position: Top-left=3, Center=2, Bottom-right=1

Highest weight should be primary content.
If chrome/status/badge scores highest → hierarchy issue.
```

### Common Anti-Patterns

| Anti-Pattern | Example | Fix |
|-------------|---------|-----|
| Date Dominance | "Sep 1982" in title3.bold | Caption or status circle |
| Header Stack | Logo+title+subtitle+search+picker+buttons | Collapse to essentials |
| Badge Overload | 5 status badges on one row | Prioritize, hide secondary |
| Combined Controls | Two functions in one button | Separate into distinct controls |
| Permanent Hints | "Tap any field to edit" on every visit | Show once, then remove |
| Negative Numbers | "-15,892 days" for expired items | "Expired" label or icon |
| Orphaned Chrome | Parent header visible on child view | Ensure child replaces parent |
| Giant Padding | 100pt padding for 44pt floating button | Match to actual element size |
| Hardcoded Colors | `Color.white` in dark mode | Use semantic `.background` |
| Heavy View Body | 200-line body with inline logic | Extract computed properties |

</ui-enhancer>
