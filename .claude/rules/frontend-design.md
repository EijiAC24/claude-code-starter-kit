---
paths: src/**/*.{tsx,jsx}, **/*.css, **/*.scss, **/*.module.css
---

# Frontend Design Guidelines

> Avoid generic "AI slop" aesthetics. Create distinctive, polished interfaces.

## Typography

### Avoid Default Choices
❌ Inter, Roboto, Open Sans (overused)
✅ Distinctive alternatives:
- **Headlines**: Bricolage Grotesque, Space Grotesk, Manrope
- **Body**: Source Sans 3, IBM Plex Sans, Nunito
- **Monospace**: JetBrains Mono, Fira Code, Berkeley Mono

### Typography Scale
Use consistent ratios (1.25 or 1.333):
```css
:root {
  --text-xs: 0.75rem;    /* 12px */
  --text-sm: 0.875rem;   /* 14px */
  --text-base: 1rem;     /* 16px */
  --text-lg: 1.125rem;   /* 18px */
  --text-xl: 1.25rem;    /* 20px */
  --text-2xl: 1.5rem;    /* 24px */
  --text-3xl: 1.875rem;  /* 30px */
  --text-4xl: 2.25rem;   /* 36px */
}
```

### Line Height & Spacing
- Body text: `line-height: 1.5-1.7`
- Headings: `line-height: 1.1-1.3`
- Paragraph spacing: `margin-bottom: 1em`

---

## Color

### Avoid Clichés
❌ Purple-to-blue gradients, pure black backgrounds
✅ Distinctive palettes:
- Warm neutrals with accent colors
- Muted, sophisticated tones
- High-contrast with intentional accent

### Color System Structure
```css
:root {
  /* Semantic colors */
  --color-primary: #2563eb;
  --color-secondary: #64748b;
  --color-success: #16a34a;
  --color-warning: #ca8a04;
  --color-error: #dc2626;

  /* Neutral scale */
  --gray-50: #f8fafc;
  --gray-100: #f1f5f9;
  --gray-200: #e2e8f0;
  --gray-300: #cbd5e1;
  --gray-400: #94a3b8;
  --gray-500: #64748b;
  --gray-600: #475569;
  --gray-700: #334155;
  --gray-800: #1e293b;
  --gray-900: #0f172a;
}
```

### Accessibility
- Minimum contrast ratio: 4.5:1 (normal text), 3:1 (large text)
- Don't rely on color alone for information
- Test with color blindness simulators

---

## Layout

### Spacing System
Use consistent spacing scale (4px base):
```css
:root {
  --space-1: 0.25rem;   /* 4px */
  --space-2: 0.5rem;    /* 8px */
  --space-3: 0.75rem;   /* 12px */
  --space-4: 1rem;      /* 16px */
  --space-6: 1.5rem;    /* 24px */
  --space-8: 2rem;      /* 32px */
  --space-12: 3rem;     /* 48px */
  --space-16: 4rem;     /* 64px */
}
```

### Container Width
```css
.container {
  max-width: 1200px;
  margin-inline: auto;
  padding-inline: var(--space-4);
}

/* Responsive */
@media (min-width: 640px) { .container { max-width: 640px; } }
@media (min-width: 768px) { .container { max-width: 768px; } }
@media (min-width: 1024px) { .container { max-width: 1024px; } }
@media (min-width: 1280px) { .container { max-width: 1280px; } }
```

---

## Motion & Animation

### Principles
- Use motion purposefully (not decoratively)
- Keep durations short: 150-300ms for UI, 300-500ms for emphasis
- Prefer `transform` and `opacity` (GPU-accelerated)
- Respect `prefers-reduced-motion`

### Standard Easings
```css
:root {
  --ease-in: cubic-bezier(0.4, 0, 1, 1);
  --ease-out: cubic-bezier(0, 0, 0.2, 1);
  --ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
  --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1);
}
```

### Common Patterns
```css
/* Fade in */
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* Slide up */
@keyframes slideUp {
  from { transform: translateY(10px); opacity: 0; }
  to { transform: translateY(0); opacity: 1; }
}

/* Scale */
.button:hover {
  transform: scale(1.02);
  transition: transform 150ms var(--ease-out);
}
```

### Reduced Motion
```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

---

## Component Patterns

### Buttons
```css
.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-2);
  padding: var(--space-2) var(--space-4);
  border-radius: 0.375rem;
  font-weight: 500;
  transition: all 150ms var(--ease-out);
}

.button:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}
```

### Cards
```css
.card {
  background: white;
  border-radius: 0.5rem;
  padding: var(--space-6);
  box-shadow:
    0 1px 3px rgba(0,0,0,0.1),
    0 1px 2px rgba(0,0,0,0.06);
}
```

### Form Inputs
```css
.input {
  width: 100%;
  padding: var(--space-2) var(--space-3);
  border: 1px solid var(--gray-300);
  border-radius: 0.375rem;
  transition: border-color 150ms, box-shadow 150ms;
}

.input:focus {
  border-color: var(--color-primary);
  box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
  outline: none;
}
```

---

## Theme Support

### CSS Variables for Theming
```css
:root {
  --bg-primary: white;
  --bg-secondary: var(--gray-50);
  --text-primary: var(--gray-900);
  --text-secondary: var(--gray-600);
}

[data-theme="dark"] {
  --bg-primary: var(--gray-900);
  --bg-secondary: var(--gray-800);
  --text-primary: var(--gray-50);
  --text-secondary: var(--gray-400);
}
```

---

## Quality Checklist

Before shipping UI:
- [ ] Typography hierarchy is clear
- [ ] Color contrast meets WCAG AA
- [ ] Spacing is consistent
- [ ] Interactive states (hover, focus, active) defined
- [ ] Responsive behavior tested
- [ ] Reduced motion preference respected
- [ ] Loading and error states designed
- [ ] Empty states designed
