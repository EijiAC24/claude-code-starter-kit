---
paths: src/**/*.{tsx,jsx}, **/*.css, **/*.scss, **/*.module.css, **/*.dart
---

# Frontend Design Guidelines

> Universal design principles for Web (React), Flutter, and other UI frameworks. Based on modern design systems and accessibility standards for 2025.

## Design Philosophy

- **Consistency** - Use design tokens, not magic numbers
- **Accessibility** - WCAG 2.1 AA minimum
- **Performance** - Optimize for Core Web Vitals
- **Maintainability** - Component-driven architecture

---

## Typography

### Avoid Generic Fonts
```css
/* AVOID: Overused fonts */
font-family: 'Inter', 'Roboto', 'Open Sans', sans-serif;

/* BETTER: Distinctive alternatives */
/* Headlines */
font-family: 'Space Grotesk', 'Manrope', 'Bricolage Grotesque', sans-serif;

/* Body */
font-family: 'Source Sans 3', 'IBM Plex Sans', 'Nunito', sans-serif;

/* Monospace */
font-family: 'JetBrains Mono', 'Fira Code', 'Berkeley Mono', monospace;
```

### Typography Scale (1.25 ratio)
```css
:root {
  /* Font sizes */
  --text-xs: 0.75rem;     /* 12px */
  --text-sm: 0.875rem;    /* 14px */
  --text-base: 1rem;      /* 16px */
  --text-lg: 1.125rem;    /* 18px */
  --text-xl: 1.25rem;     /* 20px */
  --text-2xl: 1.5rem;     /* 24px */
  --text-3xl: 1.875rem;   /* 30px */
  --text-4xl: 2.25rem;    /* 36px */
  --text-5xl: 3rem;       /* 48px */

  /* Line heights */
  --leading-tight: 1.25;
  --leading-snug: 1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.625;
  --leading-loose: 2;

  /* Font weights */
  --font-normal: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;
}
```

### Usage Guidelines
- Body text: `--text-base` with `--leading-relaxed`
- Headings: `--leading-tight` to `--leading-snug`
- Minimum touch target: 44px × 44px
- Minimum readable font size: 14px (16px preferred)

---

## Color System

### Semantic Color Tokens
```css
:root {
  /* Brand colors */
  --color-primary: #2563eb;
  --color-primary-hover: #1d4ed8;
  --color-primary-active: #1e40af;

  /* Semantic colors */
  --color-success: #16a34a;
  --color-warning: #ca8a04;
  --color-error: #dc2626;
  --color-info: #0891b2;

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
  --gray-950: #020617;

  /* Contextual */
  --color-background: var(--gray-50);
  --color-surface: white;
  --color-text-primary: var(--gray-900);
  --color-text-secondary: var(--gray-600);
  --color-text-muted: var(--gray-400);
  --color-border: var(--gray-200);
}
```

### Dark Mode
```css
[data-theme='dark'] {
  --color-background: var(--gray-950);
  --color-surface: var(--gray-900);
  --color-text-primary: var(--gray-50);
  --color-text-secondary: var(--gray-400);
  --color-text-muted: var(--gray-500);
  --color-border: var(--gray-800);
}
```

### Accessibility Contrast
| Text Type | Minimum Ratio |
|-----------|---------------|
| Normal text (< 18px) | 4.5:1 |
| Large text (≥ 18px bold, ≥ 24px) | 3:1 |
| UI components | 3:1 |
| Focus indicators | 3:1 |

---

## Spacing System

### 4px Base Scale
```css
:root {
  --space-0: 0;
  --space-px: 1px;
  --space-0.5: 0.125rem;  /* 2px */
  --space-1: 0.25rem;     /* 4px */
  --space-2: 0.5rem;      /* 8px */
  --space-3: 0.75rem;     /* 12px */
  --space-4: 1rem;        /* 16px */
  --space-5: 1.25rem;     /* 20px */
  --space-6: 1.5rem;      /* 24px */
  --space-8: 2rem;        /* 32px */
  --space-10: 2.5rem;     /* 40px */
  --space-12: 3rem;       /* 48px */
  --space-16: 4rem;       /* 64px */
  --space-20: 5rem;       /* 80px */
  --space-24: 6rem;       /* 96px */
}
```

### Spacing Usage
| Context | Recommended |
|---------|-------------|
| Inline elements | `--space-1` to `--space-2` |
| Form fields gap | `--space-3` to `--space-4` |
| Card padding | `--space-4` to `--space-6` |
| Section spacing | `--space-8` to `--space-16` |
| Page margins | `--space-4` to `--space-8` |

---

## Layout

### Container
```css
.container {
  width: 100%;
  max-width: 1280px;
  margin-inline: auto;
  padding-inline: var(--space-4);
}

@media (min-width: 640px) {
  .container { padding-inline: var(--space-6); }
}

@media (min-width: 1024px) {
  .container { padding-inline: var(--space-8); }
}
```

### Responsive Breakpoints
```css
:root {
  --breakpoint-sm: 640px;
  --breakpoint-md: 768px;
  --breakpoint-lg: 1024px;
  --breakpoint-xl: 1280px;
  --breakpoint-2xl: 1536px;
}
```

### Grid System
```css
.grid {
  display: grid;
  gap: var(--space-4);
}

.grid-cols-2 { grid-template-columns: repeat(2, minmax(0, 1fr)); }
.grid-cols-3 { grid-template-columns: repeat(3, minmax(0, 1fr)); }
.grid-cols-4 { grid-template-columns: repeat(4, minmax(0, 1fr)); }

/* Auto-fit responsive grid */
.grid-auto {
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
}
```

---

## Motion & Animation

### Timing Tokens
```css
:root {
  /* Durations */
  --duration-instant: 50ms;
  --duration-fast: 150ms;
  --duration-normal: 250ms;
  --duration-slow: 400ms;
  --duration-slower: 600ms;

  /* Easings */
  --ease-linear: linear;
  --ease-in: cubic-bezier(0.4, 0, 1, 1);
  --ease-out: cubic-bezier(0, 0, 0.2, 1);
  --ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
  --ease-spring: cubic-bezier(0.34, 1.56, 0.64, 1);
}
```

### Usage Guidelines
| Interaction | Duration | Easing |
|-------------|----------|--------|
| Button hover/active | 150ms | ease-out |
| Modal open/close | 250ms | ease-out |
| Page transitions | 300-400ms | ease-in-out |
| Micro-interactions | 150ms | ease-out |
| Loading spinners | 1000ms+ | linear |

### Common Animations
```css
/* Fade in */
@keyframes fadeIn {
  from { opacity: 0; }
  to { opacity: 1; }
}

/* Slide up */
@keyframes slideUp {
  from {
    opacity: 0;
    transform: translateY(8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

/* Scale in */
@keyframes scaleIn {
  from {
    opacity: 0;
    transform: scale(0.95);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}

/* Spinner */
@keyframes spin {
  to { transform: rotate(360deg); }
}
```

### Reduced Motion
```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

---

## Component Patterns

### Button
```css
.button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: var(--space-2);
  padding: var(--space-2) var(--space-4);
  min-height: 44px; /* Touch target */
  border: none;
  border-radius: 0.375rem;
  font-size: var(--text-sm);
  font-weight: var(--font-medium);
  line-height: 1;
  cursor: pointer;
  transition: all var(--duration-fast) var(--ease-out);
}

.button:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* Variants */
.button-primary {
  background: var(--color-primary);
  color: white;
}

.button-primary:hover:not(:disabled) {
  background: var(--color-primary-hover);
}

.button-secondary {
  background: transparent;
  border: 1px solid var(--color-border);
  color: var(--color-text-primary);
}
```

### Input
```css
.input {
  width: 100%;
  padding: var(--space-2) var(--space-3);
  min-height: 44px;
  border: 1px solid var(--color-border);
  border-radius: 0.375rem;
  font-size: var(--text-base);
  background: var(--color-surface);
  color: var(--color-text-primary);
  transition: border-color var(--duration-fast), box-shadow var(--duration-fast);
}

.input:focus {
  outline: none;
  border-color: var(--color-primary);
  box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
}

.input:invalid {
  border-color: var(--color-error);
}

.input::placeholder {
  color: var(--color-text-muted);
}
```

### Card
```css
.card {
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: 0.5rem;
  padding: var(--space-6);
  box-shadow:
    0 1px 3px 0 rgb(0 0 0 / 0.1),
    0 1px 2px -1px rgb(0 0 0 / 0.1);
}

.card-interactive {
  cursor: pointer;
  transition: box-shadow var(--duration-fast), transform var(--duration-fast);
}

.card-interactive:hover {
  box-shadow:
    0 4px 6px -1px rgb(0 0 0 / 0.1),
    0 2px 4px -2px rgb(0 0 0 / 0.1);
  transform: translateY(-2px);
}
```

---

## Accessibility (A11y)

### Focus Management
```css
/* Visible focus for keyboard users */
:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

/* Remove focus ring for mouse users */
:focus:not(:focus-visible) {
  outline: none;
}

/* Skip link */
.skip-link {
  position: absolute;
  top: -40px;
  left: 0;
  padding: var(--space-2) var(--space-4);
  background: var(--color-primary);
  color: white;
  z-index: 100;
}

.skip-link:focus {
  top: 0;
}
```

### Screen Reader Only
```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

### ARIA Patterns
```tsx
// Button with loading state
<button
  aria-busy={isLoading}
  aria-disabled={isDisabled}
  aria-label={ariaLabel}
>
  {isLoading ? <Spinner /> : children}
</button>

// Form field with error
<div>
  <label htmlFor="email">Email</label>
  <input
    id="email"
    type="email"
    aria-invalid={!!error}
    aria-describedby={error ? 'email-error' : undefined}
  />
  {error && (
    <span id="email-error" role="alert">
      {error}
    </span>
  )}
</div>

// Modal dialog
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="modal-title"
  aria-describedby="modal-description"
>
  <h2 id="modal-title">Title</h2>
  <p id="modal-description">Description</p>
</div>
```

---

## Framework-Specific Patterns

### React Patterns

#### Compound Components
```tsx
// Button group with context
const ButtonGroup = ({ children }: { children: React.ReactNode }) => (
  <div role="group" className="button-group">
    {children}
  </div>
);

ButtonGroup.Button = ({ children, ...props }: ButtonProps) => (
  <button className="button-group-item" {...props}>
    {children}
  </button>
);

// Usage
<ButtonGroup>
  <ButtonGroup.Button>Option 1</ButtonGroup.Button>
  <ButtonGroup.Button>Option 2</ButtonGroup.Button>
</ButtonGroup>
```

#### Polymorphic Components
```tsx
type AsProps<E extends React.ElementType> = {
  as?: E;
};

type PolymorphicProps<E extends React.ElementType, P = {}> = P &
  AsProps<E> &
  Omit<React.ComponentPropsWithoutRef<E>, keyof (AsProps<E> & P)>;

function Button<E extends React.ElementType = 'button'>({
  as,
  children,
  ...props
}: PolymorphicProps<E, { variant?: 'primary' | 'secondary' }>) {
  const Component = as || 'button';
  return <Component {...props}>{children}</Component>;
}

// Usage
<Button>Click me</Button>
<Button as="a" href="/path">Link button</Button>
```

#### forwardRef Pattern
```tsx
interface InputProps extends React.InputHTMLAttributes<HTMLInputElement> {
  label: string;
  error?: string;
}

const Input = React.forwardRef<HTMLInputElement, InputProps>(
  ({ label, error, id, ...props }, ref) => {
    const inputId = id || useId();

    return (
      <div className="input-wrapper">
        <label htmlFor={inputId}>{label}</label>
        <input
          ref={ref}
          id={inputId}
          aria-invalid={!!error}
          {...props}
        />
        {error && <span className="error">{error}</span>}
      </div>
    );
  }
);

Input.displayName = 'Input';
```

### Flutter Patterns

#### Theme Definition
```dart
// lib/core/theme/app_theme.dart
class AppTheme {
  // Typography Scale (1.25 ratio)
  static const double textXs = 12;
  static const double textSm = 14;
  static const double textBase = 16;
  static const double textLg = 18;
  static const double textXl = 20;
  static const double text2Xl = 24;
  static const double text3Xl = 30;
  static const double text4Xl = 36;

  // Spacing (4px base)
  static const double space1 = 4;
  static const double space2 = 8;
  static const double space3 = 12;
  static const double space4 = 16;
  static const double space5 = 20;
  static const double space6 = 24;
  static const double space8 = 32;

  // Animation Durations
  static const Duration durationFast = Duration(milliseconds: 150);
  static const Duration durationNormal = Duration(milliseconds: 250);
  static const Duration durationSlow = Duration(milliseconds: 400);

  // Curves
  static const Curve easeOut = Curves.easeOut;
  static const Curve easeInOut = Curves.easeInOut;
  static const Curve spring = Curves.elasticOut;
}
```

#### Color Scheme
```dart
// lib/core/theme/app_colors.dart
class AppColors {
  // Brand
  static const Color primary = Color(0xFF2563EB);
  static const Color primaryHover = Color(0xFF1D4ED8);

  // Semantic
  static const Color success = Color(0xFF16A34A);
  static const Color warning = Color(0xFFCA8A04);
  static const Color error = Color(0xFFDC2626);

  // Neutral
  static const Color gray50 = Color(0xFFF8FAFC);
  static const Color gray100 = Color(0xFFF1F5F9);
  static const Color gray200 = Color(0xFFE2E8F0);
  static const Color gray500 = Color(0xFF64748B);
  static const Color gray900 = Color(0xFF0F172A);

  // Light theme
  static ColorScheme get lightScheme => ColorScheme.light(
    primary: primary,
    error: error,
    surface: Colors.white,
    onSurface: gray900,
  );

  // Dark theme
  static ColorScheme get darkScheme => ColorScheme.dark(
    primary: primary,
    error: error,
    surface: gray900,
    onSurface: gray50,
  );
}
```

#### Accessible Button
```dart
class AppButton extends StatelessWidget {
  final String label;
  final VoidCallback? onPressed;
  final bool isLoading;

  const AppButton({
    super.key,
    required this.label,
    this.onPressed,
    this.isLoading = false,
  });

  @override
  Widget build(BuildContext context) {
    return Semantics(
      button: true,
      enabled: onPressed != null && !isLoading,
      child: SizedBox(
        height: 44, // Minimum touch target
        child: ElevatedButton(
          onPressed: isLoading ? null : onPressed,
          child: isLoading
              ? const SizedBox(
                  width: 20,
                  height: 20,
                  child: CircularProgressIndicator(strokeWidth: 2),
                )
              : Text(label),
        ),
      ),
    );
  }
}
```

#### Animated Container
```dart
class FadeSlideIn extends StatelessWidget {
  final Widget child;
  final Duration duration;
  final Offset offset;

  const FadeSlideIn({
    super.key,
    required this.child,
    this.duration = const Duration(milliseconds: 250),
    this.offset = const Offset(0, 8),
  });

  @override
  Widget build(BuildContext context) {
    // Respect reduced motion
    final reduceMotion = MediaQuery.of(context).disableAnimations;

    if (reduceMotion) {
      return child;
    }

    return TweenAnimationBuilder<double>(
      tween: Tween(begin: 0, end: 1),
      duration: duration,
      curve: Curves.easeOut,
      builder: (context, value, child) {
        return Opacity(
          opacity: value,
          child: Transform.translate(
            offset: Offset(
              offset.dx * (1 - value),
              offset.dy * (1 - value),
            ),
            child: child,
          ),
        );
      },
      child: child,
    );
  }
}
```

#### Responsive Layout
```dart
class ResponsiveBuilder extends StatelessWidget {
  final Widget mobile;
  final Widget? tablet;
  final Widget? desktop;

  const ResponsiveBuilder({
    super.key,
    required this.mobile,
    this.tablet,
    this.desktop,
  });

  static const double breakpointSm = 640;
  static const double breakpointMd = 768;
  static const double breakpointLg = 1024;

  @override
  Widget build(BuildContext context) {
    return LayoutBuilder(
      builder: (context, constraints) {
        if (constraints.maxWidth >= breakpointLg && desktop != null) {
          return desktop!;
        }
        if (constraints.maxWidth >= breakpointMd && tablet != null) {
          return tablet!;
        }
        return mobile;
      },
    );
  }
}
```

---

## Performance

### General Principles
- Use `transform` and `opacity` for animations (GPU-accelerated)
- Avoid animating: `width`, `height`, `top`, `left`, `margin`, `padding`
- Minimize rebuilds/re-renders
- Lazy load heavy components

### React Performance
```tsx
// Memoize expensive calculations
const expensiveValue = useMemo(() => {
  return computeExpensiveValue(a, b);
}, [a, b]);

// Memoize callbacks
const handleClick = useCallback(() => {
  doSomething(id);
}, [id]);

// Memoize components
const MemoizedList = React.memo(({ items }: { items: Item[] }) => (
  <ul>
    {items.map((item) => (
      <li key={item.id}>{item.name}</li>
    ))}
  </ul>
));
```

### Flutter Performance
```dart
// Use const constructors
const SizedBox(height: 16); // ✅
SizedBox(height: 16);       // ❌

// Use const widgets where possible
class MyWidget extends StatelessWidget {
  const MyWidget({super.key}); // ✅ const constructor

  @override
  Widget build(BuildContext context) {
    return const Column(
      children: [
        Text('Static text'),    // ✅ const
        Icon(Icons.star),       // ✅ const
      ],
    );
  }
}

// Avoid rebuilding entire lists
ListView.builder(           // ✅ Lazy builds items
  itemCount: items.length,
  itemBuilder: (context, index) => ItemWidget(items[index]),
);

// Use RepaintBoundary for complex animations
RepaintBoundary(
  child: ComplexAnimatedWidget(),
);
```

---

## Quality Checklist

Before shipping UI:
- [ ] Typography hierarchy is clear and consistent
- [ ] Color contrast meets WCAG AA (4.5:1 for text)
- [ ] Spacing follows the design system scale
- [ ] All interactive elements have visible focus states
- [ ] Touch targets are at least 44×44px
- [ ] Component works with keyboard navigation
- [ ] Screen reader announces content correctly
- [ ] Reduced motion preference is respected
- [ ] Loading states are designed
- [ ] Error states are designed
- [ ] Empty states are designed
- [ ] Works across target browsers
- [ ] Responsive at all breakpoints
