---
name: landing-page-creation
description: "Create polished landing pages and marketing microsites with strong information architecture, accessible responsive layouts, and conversion-aware UX."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Landing Page Creation Skill

## Capabilities
- **Plan landing-page structure** — shape the page around a clear story arc such as hero, proof, features, CTA, FAQ, and footer
- **Design conversion-aware sections** — frame copy around benefits, trust signals, objections, and clear calls to action
- **Build semantic frontends** — use accessible HTML landmarks, headings, buttons, links, and forms that work across devices
- **Implement responsive layouts** — create mobile-first sections that scale cleanly to tablet and desktop breakpoints
- **Apply visual hierarchy** — use spacing, contrast, typography, and design tokens to guide attention to the primary CTA
- **Support multiple stacks** — keep solutions usable in vanilla HTML/CSS/JS while mapping cleanly to React, Vue, Svelte, or similar frameworks
- **Optimize marketing performance** — keep pages light with lazy-loaded media, reduced motion support, and minimal JavaScript
- **Coordinate generated imagery** — when bespoke hero art or decorative assets are needed, reference the `openai-image-generation` skill for prompt and asset workflow guidance

## Workflow

Follow these steps in order for landing pages, product pages, campaign pages, and marketing microsites.

### Step 1 — Clarify the Offer
Before touching layout or code, identify:
- The audience segment and their main problem
- The single primary conversion goal (signup, purchase, demo request, waitlist, download)
- The strongest value proposition and differentiators
- The proof points available: testimonials, metrics, customer logos, guarantees, or partner badges
- The constraints: brand tokens, CMS/framework limits, available assets, and required tracking/analytics

If the prompt is thin, explicitly infer a simple conversion strategy instead of producing a generic page.

### Step 2 — Define the Information Architecture
Prefer a landing-page flow that answers the visitor's questions in order:
1. **Hero** — what this is, who it helps, and what action to take now
2. **Social proof / trust** — logos, review snippets, outcome metrics, or security/compliance cues
3. **Core features / benefits** — explain outcomes first, then supporting details
4. **How it works / process** — reduce uncertainty with a short sequence or explainer
5. **CTA reinforcement** — repeat the primary action after enough trust has been built
6. **Footer** — include contact, legal, secondary navigation, and low-friction trust details

Optional sections include pricing, FAQ, comparison, feature deep-dives, integration logos, and lead-capture forms.

### Step 3 — Translate Copy into Sections
Each section should do one job:
- Lead with **benefit-first headlines**, not internal product jargon
- Use supporting body copy that answers "why should I care?"
- Pair trust-sensitive claims with proof, examples, or numbers
- Keep CTA labels specific: `"Start free trial"`, `"Book a demo"`, `"See pricing"`
- Match imagery and icons to the claim being made, not just decoration

### Step 4 — Implement with Semantic, Accessible Markup
Build with:
- One clear `<main>` landmark and logical heading order
- Real buttons for actions and real links for navigation
- Descriptive alt text for meaningful imagery and empty alt text for decorative imagery
- Forms with associated labels, error states, and autocomplete where relevant
- Skip-link, focus-visible states, and keyboard-reachable interactive elements

Favor simple DOM structure and reusable section patterns over deeply nested wrappers.

### Step 5 — Make It Responsive and Visually Clear
Use a mobile-first layout approach:
- Start with narrow screens and stack content vertically
- Scale up spacing, columns, and media treatment at larger breakpoints
- Keep the hero readable without relying on oversized imagery
- Use design tokens or CSS variables for colors, spacing, radius, and typography
- Preserve clear contrast and consistent CTA styling across all sections

### Step 6 — Keep It Fast
For marketing pages, performance is part of UX:
- Use appropriately sized images and modern formats when possible
- Lazy-load non-critical images below the fold
- Avoid heavy animation libraries unless the page truly needs them
- Respect `prefers-reduced-motion`
- Minimize JavaScript and defer non-critical behavior
- Avoid layout shifts from missing width/height or unstable async content

### Step 7 — QA the Experience
Before calling the page done, verify:
- Anchor links navigate to the intended section
- Keyboard navigation is complete and focus order is sensible
- Contrast is acceptable for body text, links, and CTA states
- Layout holds on mobile, tablet, and desktop widths
- CTA buttons and forms work as expected
- Images, badges, and proof sections still support the conversion story instead of distracting from it

## Best Practices
1. **One primary CTA per page** — secondary actions may exist, but the page should clearly favor one conversion goal.
2. **Benefit before feature** — start with the outcome users get, then explain the mechanism.
3. **Trust near decision points** — place testimonials, guarantees, or proof close to CTAs and forms.
4. **Tokens over one-off styling** — use shared spacing, color, radius, and typography tokens to keep the page cohesive and easier to tune.
5. **Progressive enhancement** — the page should still communicate and convert even if optional JavaScript fails.
6. **Use motion sparingly** — animation should support hierarchy, not compete with the message.
7. **Reuse section patterns** — hero, proof strip, feature grid, FAQ, and CTA bands should be modular so they port cleanly between projects.

## Prompt Guidance
- Specify the **audience**, **offer**, and **primary CTA** first.
- Include any required **brand tone**, **design tokens**, or reference pages.
- Mention whether the page should be **vanilla HTML/CSS/JS** or adapted to a framework.
- Provide any required sections, such as pricing, FAQ, testimonials, newsletter signup, or contact capture.
- If custom imagery is needed, note whether the agent should create a prompt plan using `openai-image-generation`.

## Anti-Patterns to Avoid
- ❌ Generic heroes that never explain who the page is for
- ❌ Feature dumps with no narrative or trust-building sequence
- ❌ CTA buttons with vague labels like `"Learn More"` when a stronger action is available
- ❌ Div-heavy markup that ignores landmarks, heading structure, and form semantics
- ❌ Desktop-first layouts that collapse awkwardly on phones
- ❌ Autoplay video, excessive motion, or carousels that hide key information
- ❌ Oversized unoptimized media or unnecessary JavaScript bundles
- ❌ Decorative imagery that conflicts with copy or weakens the credibility of the offer

## QA Checklist
- [ ] Hero clearly states the offer, audience, and primary CTA
- [ ] Social proof or trust cues appear before or near the first major CTA
- [ ] Headings follow a logical hierarchy
- [ ] Interactive elements are keyboard-accessible and have visible focus states
- [ ] Contrast and readability are acceptable across states and breakpoints
- [ ] Images are sized appropriately and lazy loading is used below the fold
- [ ] Broken anchors, dead buttons, and invalid form flows are fixed
- [ ] Motion respects `prefers-reduced-motion`
- [ ] Layout and CTA behavior have been checked on mobile, tablet, and desktop widths

## When to Use
- Building a standalone landing page for a product launch, waitlist, or campaign
- Creating a marketing microsite with multiple conversion-focused sections
- Translating brand messaging into a semantic, responsive frontend
- Reviewing an existing landing page for conversion UX, accessibility, and layout quality
- Adding a polished marketing page to an app, prototype, or game-related web experience
