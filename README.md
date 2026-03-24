# UI Enhancer v3.0

Systematic iOS/SwiftUI UI audit skill for [Claude Code](https://claude.com/claude-code). Analyzes screenshots and code together, recommends improvements with design-intent awareness, and applies changes incrementally with revert safety.

## What It Does

Given a screenshot and/or SwiftUI file, UI Enhancer runs an 11-domain analysis covering space efficiency, visual hierarchy, color usage, interaction patterns, accessibility, HIG compliance, dark mode, performance, design system compliance, and cross-view consistency. It interviews you first to understand your priorities and sacred elements, then generates findings with exact code changes — applied one at a time so you can keep, revert, or compact each one.

### What Makes v3.0 Different

- **Color Audit (Domain 11)** — Detects monochromatic "flat wall" views (common in forms/settings), maps every color and opacity in the view, flags inconsistencies, and recommends fixes that break the visual monotony without breaking your design system
- **Adaptive View Profile** — Learns your project's color conventions and component usage patterns across audits. On the first run it builds a baseline; on subsequent runs it flags deviations from established patterns
- **Cross-View Consistency Additions** — Doesn't just tell you what to remove — detects features that are *missing* based on what sibling views include (e.g., "6 of 8 views enable showThemeToggle — this one doesn't")
- **Platform-Specific Heuristics** — macOS dashboards with sidebars should be summaries, not launchers. iPhone dashboards without sidebars need the hub pattern. The skill knows the difference
- **Space Budget** — Quantified before/after table showing exactly how much vertical space was recovered
- **Dead Code Cleanup** — After removing UI elements, flags any definitions that became unreferenced
- **Refinement History** — Tracks what spacing/color/layout values were tried and kept, so future sessions can reference past decisions
- **Batch Mode** — Audit all views in a directory, rank by severity, build the View Profile in one pass

## Install

Copy the `ui-enhancer` folder to your Claude Code skills directory:

```bash
cp -r ui-enhancer ~/.claude/skills/
```

## Commands

```
FULL AUDIT
  /ui-enhancer              Full 11-domain audit with interview

SINGLE DOMAIN
  /ui-enhancer space        Space efficiency
  /ui-enhancer hierarchy    Visual hierarchy
  /ui-enhancer density      Information density
  /ui-enhancer interaction  Interaction patterns
  /ui-enhancer accessibility  Accessibility
  /ui-enhancer hig          HIG compliance
  /ui-enhancer dark-mode    Dark mode
  /ui-enhancer performance  Performance impact
  /ui-enhancer design-system  Design system compliance
  /ui-enhancer color        Color audit (inventory, flatness, consistency)

UTILITIES
  /ui-enhancer compare      Before/after screenshot comparison
  /ui-enhancer revert       Undo changes back to checkpoint
  /ui-enhancer batch [path] Audit all views in a directory
  /ui-enhancer help         Show all commands

OPTIONS
  --capture                 Capture screenshot from running simulator
  --devices                 Analyze across device sizes
```

## How It Works

1. **Interview** — Focus, priority, sacred elements, aggressiveness level
2. **Classify** — View type (dashboard, form, list, detail, sheet) with platform-specific heuristics
3. **Analyze** — Screenshot (visual scan, content ratio) + code (structure, colors, components)
4. **Report** — Findings table with severity, effort, and recommendations
5. **Implement** — One fix at a time with keep/revert/compact after each
6. **Refine** — Iterative adjustments with design-aware push-back
7. **Profile** — Update the Adaptive View Profile for cross-view learning

## Design Philosophy

UI Enhancer recommends, it doesn't dictate. Every finding acknowledges that the designer may have made an intentional choice:

> "SAFETY has 1 card — the section header costs ~40pt for a single item."
> - **Merge into MANAGE (Recommended)** — saves ~40pt, Recall Checker still prominent with red icon
> - **Keep isolated** — if the visual separation is intentional to signal safety/urgency

It pushes back when refinements would break accessibility, colorblind safety, HIG compliance, or App Store guidelines — but defers to you after explaining the tradeoff.

## Related Skills

UI Enhancer focuses on **visual and structural analysis** — what does the view look like, how is it laid out, and is it consistent with the rest of the app? Two companion skills take a different approach by focusing on **behavioral analysis** — what happens when a user actually tries to *use* the view:

### [UI Path Radar](/ui-path-radar)

Traces every navigation path through the UI, looking for dead ends, broken promises (buttons that go nowhere), mock data left in production, and unwired integrations. Where UI Enhancer asks "does this view look right?", UI Path Radar asks "can the user actually get here, and can they get back?"

### [Roundtrip Radar](/roundtrip-radar)

Follows data through complete user journeys — from input to storage to display and back. Catches bugs that pattern-based auditors miss: a form that saves data but the list never refreshes, a delete that removes from the UI but not the database, a sync that uploads but never downloads. These are the bugs that survive code review because each file looks correct in isolation.

**Why all three matter:** UI Enhancer catches visual problems. UI Path Radar catches navigation problems. Roundtrip Radar catches data flow problems. A view can score 10/10 on visual analysis and still have a broken delete button or an unreachable screen.

## Companion Skills

UI Enhancer is part of a 5-skill audit suite. Each skill finds different issues, and findings from one feed into the next via `.agents/ui-audit/` handoff files.

| Skill | Focus | GitHub |
|-------|-------|--------|
| **Data Model Radar** | Model layer: fields, serialization, relationships, migrations | [data-model-radar](https://github.com/Terryc21/data-model-radar) |
| **UI Path Radar** | Navigation paths, dead ends, broken promises | [ui-path-radar](https://github.com/Terryc21/ui-path-radar) |
| **Roundtrip Radar** | Data safety, error handling, round-trip completeness | [roundtrip-radar](https://github.com/Terryc21/roundtrip-radar) |
| **UI Enhancer** (this skill) | Visual quality, spacing, color accessibility, layout | [ui-enhancer](https://github.com/Terryc21/ui-enhancer) |
| **Capstone Radar** | Unified grading, ship/no-ship decision | [capstone-radar](https://github.com/Terryc21/capstone-radar) |

### How They Work Together

```
data-model-radar  -->  ui-path-radar  -->  roundtrip-radar
 (foundation)          (navigation)        (data flows)
                            |                   |
                            v                   v
                               ui-enhancer
                               (visual quality)
                                     |
                                     v
                              capstone-radar
                              (unified grade + ship/no-ship)
```

**Recommended audit order:**
1. **Data Model Radar** first — verify model definitions before auditing flows
2. **UI Path Radar** second — find all entry points and broken paths
3. **Roundtrip Radar** third — audit data flows behind those paths
4. **UI Enhancer** fourth — polish views after structural issues are fixed
5. **Capstone Radar** last — unified grade and ship/no-ship decision

Each skill writes a handoff YAML file to `.agents/ui-audit/` with findings relevant to the other skills. Skills read these handoffs on startup and incorporate them as suspects. If a companion skill isn't installed, the handoff file sits harmlessly until it is.

## Requirements

- Claude Code CLI
- SwiftUI project (iOS, macOS, or multiplatform)
- Screenshot and/or file path for the view to audit

## License

MIT
