# CLAUDE.md — zachellerbrook.com

Operating notes for this repo. Read this before doing anything.

## What this is

Zach Ellerbrook's personal site: portfolio front, blog behind. Zach is a Navy musician separating June 30, 2027, moving into cybersecurity. Target: purple team or security-awareness trainer, Chicago.

His technical résumé is scattered and he has no industry work history yet. This site is how he compensates: proof of work, in public, over time.

Two audiences, in this order:

1. A recruiter with 30 seconds who wants to know if he's real.
2. That same person 10 minutes later, deciding whether they like how he thinks.

**Live by Aug 16, 2026.** Little-by-little work. This site must not become the project that replaces the projects. If scope starts creeping, say so.

## Writing

`anti-ai-writing-style.md` in this repo is the source of truth for all prose. Read it before writing a single sentence of copy.

Hard rules, no exceptions:

- Section 3A banned vocabulary
- Section 3F negative parallelisms ("This isn't X, it's Y" and every variant). One instance means rewrite the sentence.
- No em dashes
- Sentence case in headers, not title case

Section 5 is the anti-overfitting guide. Don't apply the doc so rigidly that the writing reads as an AI imitating Zach.

Audit every paragraph against the doc before showing it to him.

## Design

Reference screenshots live in `docs/design-refs/`. Look at them before writing CSS.

What the references have in common: text-forward, generous white space, narrow measure (roughly 60-70 characters), serif or high-contrast type, lowercase or small-caps nav, links as the primary interface, near-zero decoration. Content carries the page.

Banned, because these are the fingerprints of an AI-generated site:

- Gradient hero backgrounds
- Three-card feature grids
- Emoji section headers
- `rounded-2xl` on everything
- Glassmorphism, animated blobs, decorative stock illustration
- "Let's build something together" and similar CTA filler
- Rows of brand-colored third-party buttons (including "Ask AI About Me" links)

## Never publish

- **Target salary or comp expectations.** Comp targets are planning context only. It never appears on the site, in `llms.txt`, in structured data, or in any post.
- Separation-date countdown framed as urgency or availability pressure.
- Anything that reads as asking to be vouched for rather than showing the work.

The writing doc's instincts apply visually too. Puffery becomes an inflated hero. Rule of three becomes the three-card grid. Metronome rhythm becomes evenly weighted sections where nothing is emphasized.

**Locked constraints:** _(to be filled in after Zach signs off on the type pairing, palette, and spacing scale — do not start building until these are agreed and written here)_

## Technical

- Static HTML/CSS. No framework unless justified to Zach first. Minimal JS.
- Semantic HTML, mobile-first, accessible, fast.
- Run the `modern-web-guidance` skill before any HTML/CSS/client-side JS work. Not optional.
- Deploy static to Cloudflare Pages or GitHub Pages. **Do not** make Zach's MacBook the public host. Self-hosting a mirror at home is a separate follow-on project, not part of launch.
- Public GitHub repo is itself part of the portfolio. Real README, sensible commit messages, steady commits rather than one giant dump.

### Machine readability

Recruiters and hiring managers increasingly ask an AI assistant before they open a browser. Build for that, invisibly:

- Semantic HTML with real elements (`article`, `section`, `nav`, `time`, `address`), not div soup.
- Full metadata per page: title, description, canonical URL, Open Graph, Twitter Card.
- Structured data (JSON-LD `Person` and `BlogPosting`).
- `llms.txt` at the root: bio, focus areas, projects, links.
- `robots.txt` with sitemap location, and a real `sitemap.xml`.

All of this is free, adds no visual weight, and costs nothing at launch. Do it.

**Not doing:** "Ask AI About Me" buttons that deep-link to ChatGPT/Claude/Perplexity with a prefilled prompt. Revisit only if Zach's public footprint grows enough that an AI would return something accurate. Reasons in the design notes below.

## Content plan

Blog through-line, singular: a professional musician learning to build technical systems in public, using AI as a tool he's deliberately trying not to become dependent on. Musician-to-cyber is the setup. The Navy exit is the clock. The AI-collaboration struggle is the craft story and the real differentiator.

Register: honest, framed as method. Documenting a practice, not confessing. Never inspirational, never LinkedIn-voice.

Launch posts:

1. Why I'm writing in public — rough notes in `post-1-draft-rough-notes.odt`
2. SSH detector, milestones 1-2 — technical proof
3. Learning to talk to the machine — the AI-collaboration post

## Working with Zach

Direct, plain-spoken. No fluff, no gamification, no cheerleading. He's an intermediate Linux user: use real professional vocabulary, explain the why, flag risks before he acts.

Never be confidently wrong. Say when you're unsure.

Use AskUserQuestion when the brief is unclear rather than filling gaps with plausible-sounding filler.

If he drifts toward cert-chasing, redesigns, or side quests, pull him back to shipping.
