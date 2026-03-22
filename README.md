# UI Enhancer

A Claude Code skill that analyzes iOS/SwiftUI views from screenshots + code and implements UI improvements with tests and revert safety.

## What It Does

Share a screenshot, a file path, or both. A brief interview focuses the audit on what matters most, then you get prioritized recommendations across 9 domains with exact code diffs, tests, and one-command revert.

### Workflow

```
Interview → Screenshot + Code Analysis → 9-Domain Audit → Report with Scores
    → ASCII Before/After Mockups → Implementation Playbook → Apply with Keep/Revert
    → Refinement with Push-Back → Generate Tests → Progress Tracking
```

### Domains

| Domain | What It Checks |
|--------|---------------|
| **Space Efficiency** | Content-to-chrome ratio, header overhead, wasted vertical space, mergeable sections |
| **Visual Hierarchy** | What draws the eye first, competing elements, typography balance |
| **Information Density** | Too sparse vs. too cluttered, redundant data, hidden useful info |
| **Interaction Patterns** | Touch targets, ambiguous buttons, gesture-only actions, dead ends |
| **Accessibility** | Color-only info, fixed fonts, contrast, VoiceOver order, Dynamic Type |
| **HIG Compliance** | Navigation patterns, system controls, platform consistency |
| **Dark Mode** | Hardcoded colors, background assumptions, shadow/contrast issues |
| **Performance** | Heavy view bodies, missing lazy containers, excessive state |
| **Design System** | Project-specific patterns, color palette, component usage |

Plus **Competitive Comparison** when a competitor screenshot is provided.

## Interactive by Design

UI Enhancer is a conversation, not a batch process. Every phase involves your input:

- **Interview** -- You set the focus, priority, sacred elements, and aggressiveness before analysis begins
- **Input choice** -- Screenshot only, code only, both, or auto-capture from simulator
- **Report review** -- You see scored findings before any code changes
- **Incremental apply** -- Each fix is applied one at a time; you keep or revert each individually
- **Refinement loop** -- After all fixes, you can tweak any element (color, size, spacing) with design-aware feedback
- **Compaction menu** -- When removal is recommended, you choose: compact (smaller), remove, or keep as-is

Nothing is batch-applied without your consent. You can stop at any point and keep what you have.

## The Interview Process

Before any analysis, UI Enhancer runs a focused interview (typically 30 seconds) to calibrate the audit.

### Focus and Priority

Two questions establish scope:

| Question | Options |
|----------|---------|
| **Focus** -- Why are you reviewing? | General polish, Specific problem, Pre-release review, Competitive parity |
| **Priority** -- Which aspects? | All 9 domains, Visual (layout/hierarchy), Interaction (usability), Technical (performance/compliance) |

If you report a specific problem (e.g., "too much wasted space"), that problem gets Critical severity minimum and the audit focuses there first. If you choose competitive parity, you can provide a competitor screenshot for side-by-side comparison.

### Design Intent

Two additional questions protect what matters to you:

| Question | Options |
|----------|---------|
| **Sacred elements** | No sacred elements, I'll point them out, Keep all branding/headers, Show me options |
| **Aggressiveness** | Conservative (tighten only), Moderate (compact + remove redundancies), Aggressive (maximize space) |

Elements you mark as sacred get tagged `[PRESERVE]` and are compacted (made smaller) rather than removed. The aggressiveness setting affects spacing reduction, header treatment, and whether decorative elements are kept or removed.

### Free-form Constraints

After structured questions, you can add anything: "preserve saturation on card backgrounds," "don't touch the toolbar," "match the style of DonateView." These apply as rules throughout the entire audit.

You can answer all questions at once (e.g., "1, 1, 1, 2") or one at a time.

## Safety Guardrails

UI Enhancer includes multiple layers of protection to prevent unintended changes.

### Git Checkpoint

Before making any code changes, UI Enhancer offers to create a safety checkpoint:

- **Commit first** -- Commits uncommitted work so you can revert to a clean state
- **Skip** -- Proceeds without committing (if your tree is clean or you manage git yourself)
- **File backup** -- If git isn't available, copies each file to `.backup` before editing

UI Enhancer never force-pushes, never reverts past your commits, and never modifies files without the checkpoint offer.

### Incremental Apply with Keep/Revert

Every finding is applied one at a time. After each change:

```
Fix #3 applied: Compact illustrated header (140pt → 56pt). Keep this change?
  → Keep
  → Compact instead (revert removal, apply smaller version)
  → Revert this fix (undo just this change, continue with others)
  → Revert all (undo everything back to checkpoint)
  → Stop here (keep what you have, skip remaining fixes)
```

### Content Preservation

Before removing any UI element, UI Enhancer checks:

- Does it contain explanatory text a first-time user needs?
- Does it contain status information?
- Is it tagged `[PRESERVE]` from the interview?

If content would be lost, it offers relocation options (help button, first-visit-only display, nav subtitle) rather than silent deletion.

### Element Compaction

When removal is recommended for a branded or decorative element, compaction is always offered as the default alternative:

| Element Type | Full Size | Compacted | Savings |
|---|---|---|---|
| Illustrated header | ~140pt | ~56pt (inline icon + title) | ~84pt |
| Section header | ~48pt | ~32pt (smaller icon, tighter padding) | ~16pt |
| Photo/hero row | ~120pt | ~44pt (thumbnail inline with title) | ~76pt |
| Status banner | ~80pt | ~32pt (compact badge/chip) | ~48pt |

This preserves visual identity at reduced size instead of removing it.

### Cross-View Consistency

Before removing or modifying a shared component, UI Enhancer greps the codebase to count how many views use it. If a component appears in 3+ views:

> "ContentIllustratedHeader is used in 8 views. Removing it here would break visual consistency. Compact instead, or apply across all 8 views?"

### Design-Aware Push-Back

During the refinement phase, UI Enhancer pushes back on changes that would:

| Violation | Response |
|---|---|
| Break colorblind safety | "Green isn't colorblind-safe. How about cyan or blue?" |
| Violate color palette | "Not in the project palette. Closest approved: pink or purple." |
| Use fixed font sizes | "Fixed sizes don't scale with Dynamic Type. Use .caption instead." |
| Break cross-view consistency | "This icon circle is used in 8 other views. Resize instead?" |
| Undo the audit's improvement | "That restores ~84pt we just recovered. Compact is the middle ground." |
| Create small touch targets | "HIG requires 44pt minimum. I can make it visually 30pt with a 44pt tap area." |
| Risk App Store rejection | "Removing this dismiss button creates a dead end (guideline 4.0)." |

It explains why, offers an alternative, and defers to your judgment if you insist.

### App Store Review Guardrails

UI changes known to trigger App Store rejections are flagged before implementation:

| Risk | What Triggers It |
|------|-----------------|
| Missing accessibility | Removing VoiceOver labels or Dynamic Type support |
| Non-functional UI | Placeholder buttons or "coming soon" labels |
| iPad layout broken | Hardcoding widths that break split view |
| Missing dismiss | Removing dismiss buttons from sheets (dead end) |
| Misleading UI | Icons suggesting features the app doesn't have |
| Minimum font size | Text below 11pt that doesn't scale |
| Privacy UI | Removing permission explanations |

### Diminishing Returns

After 3 refinements on the same element, UI Enhancer suggests moving on -- preventing infinite micro-adjustment loops while respecting your autonomy.

### Multiplatform Guardrail

For views with `#if os()` blocks, UI Enhancer analyzes each platform separately and checks the other platform after every change. It won't apply iPhone heuristics (44pt touch targets, 852pt viewport) to macOS code, and warns if a change would create duplicate controls across platforms.

## Usage

```
FULL AUDIT
  /ui-enhancer              Full 9-domain audit with interview

SINGLE DOMAIN (skip interview, run one domain)
  /ui-enhancer space        Space efficiency analysis
  /ui-enhancer hierarchy    Visual hierarchy analysis
  /ui-enhancer density      Information density analysis
  /ui-enhancer interaction  Interaction patterns analysis
  /ui-enhancer accessibility  Accessibility audit
  /ui-enhancer hig          HIG compliance check
  /ui-enhancer dark-mode    Dark mode audit
  /ui-enhancer performance  Performance impact analysis
  /ui-enhancer design-system  Design system compliance

UTILITIES
  /ui-enhancer compare      Compare before/after screenshots
  /ui-enhancer revert       Undo all changes back to checkpoint
  /ui-enhancer batch [path] Audit all views in a directory
  /ui-enhancer help         Show all commands

OPTIONS (combine with any command)
  --capture                 Capture screenshot from running simulator
  --devices                 Analyze layout across device sizes
```

## Example Output

```
UI Enhancer Report: ItemDetailView
Focus: Specific problem — too much wasted space

| # | Domain | Finding | Severity | Effort |
|---|--------|---------|----------|--------|
| 1 | Space | 45% header chrome | Critical | Medium |
| 2 | Hierarchy | Date dominates title | High | Small |
| 3 | Interaction | Combined buttons | High | Small |

UX Score: 6.4/10 → 8.1/10 (after fixes)
Content ratio: 39% → 63%
Findings resolved: 8/12
Tests added: 5
```

## Install

Add to your project's `.claude/settings.json`:

```json
{
  "skills": ["/path/to/ui-enhancer"]
}
```

Or copy the `skills/ui-enhancer` directory to `~/.claude/skills/` for global availability.

## Requirements

- Claude Code CLI
- iOS/SwiftUI project (optimized for, works with UIKit too)
- Git (for revert safety; file backup fallback available without git)

## License

MIT -- See [LICENSE](LICENSE)
