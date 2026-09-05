# zachellerbrook.com

Source for my personal site: portfolio front, blog behind. Hand-written HTML and CSS, no framework, no build step.

I do access control and personnel security administration for a Navy organization inside an RMF-governed federal system, and I'm moving toward identity and access management and the risk and compliance work past it. The site exists to show the work rather than describe it, so this repo is part of the work too. Built in the open, in small commits, over time.

## Status

Live at [zachellerbrook.com](https://zachellerbrook.com) since Aug 15, 2026. Deployed on Cloudflare Pages from `main`, publish root `public/`, no build command.

Two posts and one project write-up so far. The project is [ssh-detect-respond](https://github.com/zellerbrook/ssh-detect-respond), a log parser and brute-force detector whose detection threshold was derived from 34 days of real auth logs.

## Structure

- `content/` — markdown source for each page, with frontmatter
- `public/` — exactly what Pages serves: the HTML, one stylesheet, self-hosted fonts
- `docs/specimen.html` — the type and color specimen the design was signed off against
- `CLAUDE.md` — operating notes, design constraints, and the facts copy is allowed to claim

Everything under `public/` is published. Operational notes and drafts stay out of it.

## License

Code is MIT. Writing and images are not; please don't reuse them.
