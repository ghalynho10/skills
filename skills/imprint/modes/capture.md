# /imprint capture (extract patterns from one component)

Capturing the visual patterns from one component, whether named directly or found automatically. Follow it fully.

## What this mode does

**Your role:** the person who looks at a finished component and writes down exactly what would need to match for the next one to belong beside it. Not a design critic. You are not judging whether the component looks good; you are recording what it actually is, precisely enough that it can be matched later.

## Execution

### Step 1: Find what was just built

**If a filepath was given**, read that file directly.

**If nothing was given**, identify which component files were most recently created or modified in this session. Look in the project's components directory and any other locations where UI files typically live. Read those files.

**If it is unclear which files to capture from**, ask plainly:

> "Which component should I capture patterns from?"

Do not guess at this one. Capturing from the wrong file writes a wrong entry into a record other sessions will trust.

### Step 2: Extract what matters for consistency

Read the component code. Extract only the values that affect visual consistency across the interface, not everything the file contains.

**Extract these:**

- Background, for the container, cards, panels
- Border color, width, style
- Border radius
- Text colors, primary, secondary, muted
- Text sizes and weights, for headings, body, labels, captions
- Spacing, padding inside the component, gap between elements
- Interactive states, hover, focus, active
- Shadow, if used
- Any accent or brand color usage

**Do not extract these**, they are too context dependent to be a consistency rule:

- Width and height
- Flex and grid layout, structural rather than visual
- Positioning, absolute, relative, stacking order
- Animation and transition timing, unless it is a deliberate pattern worth enforcing everywhere
- Responsive breakpoint variants, capture the base pattern only

### Step 3: compare against the baseline before writing

If `ui-registry.md` already has a `## Baseline` section (written by a previous `audit` run), read it now and compare what you extracted against it, property by property.

This step exists because capture without it will happily record a mistake as a pattern. A component built with the wrong radius, or a hardcoded color that bypassed the token set, becomes a registry entry that future components are told to match. On a young project that is harmless. On a mature one it is how a design system quietly erodes, one accepted outlier at a time.

**Where the component matches the baseline**, write the entry normally.

**Where it deviates**, do not silently record the deviation as the new pattern. Report it and ask:

> "This component uses <value> for <property>, but the baseline is <baseline value>. Is this a deliberate exception, or should the component be fixed to match? I will record it either way, but they are recorded differently."

- **Deliberate exception** → write the entry with the deviation, and note in `Pattern notes` that it is a known exception and why. A recorded exception is fine; an unrecorded one is drift.
- **Should be fixed** → say so plainly, list what needs changing, and do not write the entry until the component matches. Capturing a component you already know is wrong puts a wrong value in the file other sessions trust.

If no baseline exists yet, skip this step and write the entry. On a project with no established baseline there is nothing to deviate from, which is exactly what `audit` mode is for.

### Step 4: write to `ui-registry.md`

Open `ui-registry.md`. If it does not exist, create it.

Add a new entry for the component captured. Do not overwrite the file; append. If an entry for this component type already exists, update it in place rather than duplicating.

Entry format:

```
### <Component name>

File: <filepath>
Last updated: <date>

| Property | Value |
| --- | --- |
| Background | <value> |
| Border | <value> |
| Border radius | <value> |
| Text, primary | <value> |
| Text, secondary | <value> |
| Spacing | <value> |
| Hover state | <value> |
| Shadow | <value or none> |
| Accent usage | <value or none> |

Pattern notes: <any pattern decision worth noting, why a specific value was chosen, what this component should always match, what variation is allowed>
```

Values are whatever the project's styling approach actually uses, a utility class, a CSS custom property, a design token name, a raw value. Write what the code actually contains.

### Step 5: Confirm what was captured

Report plainly:

```
Imprinted <component name> into ui-registry.md

Captured:
Background: <value>
Border: <value>
Radius: <value>
Text: <values>
Spacing: <values>
Hover: <value>

Any future component of this type should match these values.
```

If anything looked inconsistent or surprising while extracting, for instance a hardcoded hex value where the rest of the project uses tokens, flag it in one line, even in capture mode. Do not fix it; audit mode is where fixing gets decided.

## The habit this skill depends on

A registry with ten entries is useful. A registry with thirty is powerful. A registry that is sometimes updated is unreliable, because nobody can tell whether a missing entry means the component does not exist or was simply never captured. Build a component, run `/imprint`, move on, every time, without exception.