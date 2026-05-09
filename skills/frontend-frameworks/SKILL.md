---
name: frontend-frameworks
description: "React, Vue, Angular, and Svelte patterns including state management, component design, and accessibility."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Frontend Frameworks Skill

## Capabilities
- **Component architecture** — atomic design (atoms, molecules, organisms, templates, pages)
- **State management** — Redux/Zustand (React), Pinia/Vuex (Vue), NgRx (Angular), Svelte stores
- **Routing patterns** — client-side routing, nested routes, route guards, lazy-loaded routes
- **Form handling & validation** — controlled/uncontrolled forms, schema validation (Zod, Yup, Valibot)
- **SSR/SSG** — server-side rendering and static site generation with Next.js, Nuxt, SvelteKit
- **Styling approaches** — CSS-in-JS, CSS Modules, Tailwind CSS, Styled Components
- **Accessibility (a11y) compliance** — ARIA attributes, keyboard navigation, screen reader support
- **Responsive design** — mobile-first, breakpoints, container queries
- **Performance optimization** — code splitting, lazy loading, memoization, virtualization

## Best Practices
1. **Single responsibility components** — each component should do one thing well
2. **Lift state up / use context sparingly** — share state at the lowest common ancestor; avoid over-using global state
3. **Prefer composition over inheritance** — compose components from smaller building blocks
4. **Use TypeScript for type safety** — type props, events, and state for better DX and fewer runtime errors
5. **Test components** — use React Testing Library, Vue Test Utils, or framework-appropriate tools
6. **Follow WCAG 2.1 AA for accessibility** — semantic HTML, ARIA labels, keyboard navigation, color contrast
7. **Use semantic HTML** — `<nav>`, `<main>`, `<article>`, `<button>` instead of generic `<div>` wrappers
8. **Lazy load routes and heavy components** — reduce initial bundle size for faster first paint
9. **Minimize bundle size** — tree-shake unused code, analyze bundles with webpack-bundle-analyzer or similar

## When to Use
- Building web UIs with React, Vue, Angular, or Svelte
- Implementing component libraries or design systems
- Setting up frontend architecture for new projects
- Reviewing frontend code for quality and accessibility
- Optimizing frontend performance (bundle size, rendering)

## Anti-Patterns to Avoid
- ❌ Prop drilling through many levels — use context, state management, or composition instead
- ❌ Massive monolithic components — break into smaller, focused components
- ❌ Direct DOM manipulation in framework code — let the framework manage the DOM
- ❌ Ignoring accessibility — all interactive elements must be keyboard-accessible and screen-reader-friendly
- ❌ Inline styles for theming — use a design token system or CSS variables
- ❌ Missing error boundaries — unhandled errors should not crash the entire UI
