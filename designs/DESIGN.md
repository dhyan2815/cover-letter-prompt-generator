# What is DESIGN.md?

The DESIGN.md concept was introduced by Google Stitch. Stitch uses a plain markdown file to describe design patterns, colors, typography, spacing, components, and hands it to an AI agent so it can generate consistent UI. No Figma plugin, no JSON schema, just a markdown file the agent reads before it writes code.

We built the VoltAgent/awesome-design-md repository on top of that idea. getdesign.md is the web app version of that repo. You can browse, preview, and copy any DESIGN.md file without cloning anything.

## The problem: AI builds "nice" but not "yours"

Tell any AI agent to "build me a landing page" and you already know what you'll get. Rounded cards. A purple-blue gradient. A centered hero. A "Get Started" button. It works. It also looks like everything else.

The reason is simple. The agent's idea of "good design" is an average of averages. It has no clue why Vercel uses border instead of shadow, why Linear keeps its letter-spacing so tight, or why Stripe goes easy on gradients. Even if it did know, cramming all of that into a prompt is borderline impossible.

So you end up with two bad options:

1. Write 40 lines of prompt every time ("use #0070f3 for links, -0.02em letter spacing on headings, 8px border radius, no shadows just 1px borders...") and still get half of it wrong.
2. Screenshot a site, paste it, say "make it look like this." The agent copies pixels but misses the system behind them.

Neither scales.

## The fix: DESIGN.md

A DESIGN.md file describes a design system semantically. It is not a token list. Not a Figma export. Not a component library. Picture a document where an experienced designer explains a brand's visual language to a developer who's seeing it for the first time. That's what it reads like.

Here's what goes inside:

- **Visual theme and atmosphere** tells the agent what the brand looks like and, more importantly, why. The philosophy behind the aesthetic. Sentences like "Minimalism as engineering principle." The agent gets intent, not just instructions.
- **Color palette and roles** gives every color a hex value and a job. "#ff5b4f, ship red, used for the production deploy flow because shipping should feel urgent." The name tells you what the color does.
- **Typography rules** cover font, size, weight, line-height, letter-spacing. But the real value is context: which style goes where, and why. "Display sizes get -2.4px tracking because headlines should feel like minified code."
- **Spacing, shadows, motion, components** fill in the rest of the system. Every rule, wherever possible, with a reason attached.

DESIGN.md keeps token, rule, and rationale in the same file. A token tells you what to use but not where. A rule tells you where but not when to bend it. The rationale is what lets an agent make the right call when it hits a situation the file never covers.

## Why markdown?

Because it is the language AI agents speak best. They can read JSON tokens but can't interpret them. They can't see Figma files. They'll imitate a screenshot but won't systematize it. Markdown sits in the middle: readable by humans, parseable by machines, easy to version and diff, and you can drop it in a repo root.

Drop a DESIGN.md file in your project root and tell your agent "use DESIGN.md as reference before you write any UI." From that point on, whether you're working with Claude Code, Cursor, or Windsurf, the agent knows which font, which color, which spacing to reach for. You don't have to repeat yourself in every prompt.

## Why a collection?

Most teams don't write their own DESIGN.md from scratch. Most teams say "make it look like Linear," "give it that Stripe feel," or "keep it close to Apple." These references are real. They come up constantly.

getdesign.md collects those starting points. Inspiration files based on Vercel, Stripe, Linear, Apple, Tesla, Notion, Figma, Supabase, and dozens more, all in the same format, all comparable. Pick one, drop it into your project, tell your agent "use this file as reference." Building on top of that language with your own content and components is up to you.

The goal is not "copy Vercel." It is to give the agent a starting language. Enough context to escape the generic average and land on a specific aesthetic. From there you drift, you make it yours, you evolve it.

## What DESIGN.md is not

The name can be misleading, so this matters:

- You can't drop it in and call the theme done. It is a dictionary. The implementation still needs writing. There is no code inside, just rules. It describes what a button looks like; you or your agent still build the button.
- It is not a brand guideline PDF either. Brand guidelines are written for humans and speak too loosely for agents to act on ("our brand feels approachable yet premium"). A DESIGN.md has to be specific enough for the agent to make its next decision.
- It is not a Figma export. Figma token exports tell you "what" but skip "why." A DESIGN.md carries the rationale.
- And it is not static. When the brand evolves, the file evolves. It gets versioned, PR'd, discussed. It behaves like code.

## Mental model

You used to hire a designer and say "you know Linear, right? Give me that feel." It worked because the designer already carried the reference in their head. That shared context sat underneath every conversation you had with them.

An AI agent doesn't have that shared context. DESIGN.md writes it into a file and puts it in the agent's "head." The reference pool you built with a designer over years, you set up with an agent in two minutes.

getdesign.md is not an "asset site." It is closer to an experiment in how design languages get shared when the designer is a machine. The DESIGN.md files are the format. The site is a collection that makes that format concrete.

## The structure of a DESIGN.md file

A DESIGN.md file has 9 standard sections. Each one is a layer the agent reaches for when making a specific design decision.

### 1. Visual theme and atmosphere

The top of the file describes the brand's feel. Not tokens or pixels. Attitude and philosophy.

> **Example:** Linear's interface embodies "opinionated calm." Every surface is dark, every motion is restrained, every element earns its place through information density, not decoration. The aesthetic borrows from developer tooling: monospaced accents, tight spacing, muted palettes.

This section answers "why does it look like this?" The other 8 sections tell you what. This one tells you why.

### 2. Color palette and roles

Every color is defined with its hex value and its semantic role. The file doesn't just say "blue." It says what that blue does.

| Role | Token | Value |
|------|-------|-------|
| Background | `--bg-primary` | `#000000` |
| Surface | `--bg-surface` | `#141414` |
| Brand accent | `--accent-primary` | `#5E6AD2` |
| Destructive | `--color-danger` | `#E5484D` |
| Text primary | `--text-primary` | `#EDEDEF` |
| Border default | `--border-default` | `#2A2A2A` |

### 3. Typography rules

Font family, size hierarchy, weight, line-height, and letter-spacing, all in a table, with context for which one goes where.

| Level | Size | Weight | Line-height | Letter-spacing |
|-------|------|--------|-------------|----------------|
| Display | 52px | 500 | 1.1 | -2.4px |
| Heading 1 | 32px | 500 | 1.2 | -1.2px |
| Body | 14px | 400 | 1.6 | -0.1px |
| Caption | 12px | 400 | 1.4 | 0 |

### 4. Component styles

Style definitions for core elements like buttons, cards, inputs, navigation, and badges, including all states. Padding, border-radius, shadow, hover/focus/disabled behavior.

**Button (primary):**
- Background: `--accent-primary`
- Padding: 6px 12px
- Border-radius: 6px
- Font-size: 13px, weight 500
- Hover: brightness(1.15)
- Focus: 2px ring offset 2px
- Disabled: opacity 0.5, pointer-events none

**Card:**
- Background: `--bg-surface`
- Border: 1px solid `--border-default`
- Border-radius: 8px
- Padding: 16px
- Shadow: none (depth comes from borders only)

### 5. Layout principles

Spacing scale, grid system, container widths, whitespace approach, and border-radius scale.

- Base unit: 4px
- Spacing scale: 4, 8, 12, 16, 24, 32, 48, 64
- Max content width: 1080px
- Section gap: 64-96px
- Border-radius scale: 4px (badge), 6px (button), 8px (card), 12px (modal)

### 6. Depth and elevation

The shadow system, surface hierarchy, and elevation levels. Which layer gets which shadow, with specific rgba values.

| Level | Usage | Shadow |
|-------|-------|--------|
| Level 0 | Page bg | none |
| Level 1 | Card, panel | `0 1px 2px rgba(0,0,0,0.3)` |
| Level 2 | Dropdown | `0 4px 12px rgba(0,0,0,0.4)` |
| Level 3 | Modal, dialog | `0 8px 24px rgba(0,0,0,0.5)` |

### 7. Do's and don'ts

Design boundaries and anti-patterns. The "don't" list matters at least as much as the "do" list.

**Do:**
- Use border for separation, not shadow
- Keep letter-spacing tight on headings (-1px or more)
- Use opacity for disabled states, not gray tints

**Don't:**
- Use rounded-full on rectangular buttons
- Mix warm and cool grays in the same surface
- Use gradients on interactive elements
- Exceed 3 font weights on a single page

### 8. Responsive behavior

Breakpoints, touch target sizes, and how things collapse on smaller screens.

| Breakpoint | Width | Behavior |
|------------|-------|----------|
| Mobile | < 640px | Single column, bottom nav |
| Tablet | < 1024px | Sidebar collapses to overlay |
| Desktop | >= 1024px | Full layout with persistent nav |

- Touch target minimum: 44x44px
- Font sizes don't drop below 13px on mobile

### 9. Agent prompt guide

A quick-reference color summary and ready-to-use component prompts for the agent.

**Quick palette:** `bg=#000`, `surface=#141414`, `accent=#5E6AD2`, `text=#EDEDEF`

**Ready-to-use prompts:**
- "Create a settings page" → dark bg, grouped sections with subtle borders, toggle switches using accent color, 14px body text
- "Build a data table" → compact rows (36px height), monospaced numbers, sticky header, hover row highlight at 4% white overlay

## Why these 9 sections?

The structure is not random. It mirrors the layers an agent walks through when making a UI decision. Atmosphere answers "what should this feel like?" Color answers "which color?" Typography answers "which font, which size?" Components answer "what does this element look like?" Layout answers "where does it go?" Depth answers "which layer?" Boundaries answer "what should I avoid?" Responsive answers "what changes on small screens?" And the prompt guide answers "how do I start?"

Each section builds on the one before it. Color extends atmosphere. A component is color and typography made concrete. The do's and don'ts are the negative space of every previous rule. The order matters. Read the file top to bottom and it flows like a story.

## Disclaimer

These are not official design systems from the listed companies. They are curated starting points inspired by publicly visible design patterns. All trademarks, brand names, and design elements belong to their respective owners. These DESIGN.md files document publicly observable design patterns for educational and development purposes.