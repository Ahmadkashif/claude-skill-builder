---
name: ui-engineering
description: Build production-grade React UIs with TypeScript, Tailwind CSS, and Motion (formerly Framer Motion). Use this skill whenever the user asks to create, build, or fix any React component, page, layout, animation, interactive element, or UI of any kind — even if they don't mention specific libraries. Also trigger for requests involving hover effects, scroll animations, page transitions, micro-interactions, drag gestures, responsive layouts, or component architecture. Trigger for both Claude artifact/preview components AND real-world project code. If the user says anything like "build me a...", "make a component for...", "animate this...", "create a landing page", "design a UI", "fix this React code", or discusses any frontend work, use this skill.
---

# UI Engineering Skill

Build hallucination-free, production-grade React UIs with TypeScript, Tailwind CSS, and Motion for React.

## Why This Skill Exists

Claude has strong reasoning but unreliable recall across library versions. This skill provides verified API references so Claude works from documented truth rather than memory. **Always read the relevant reference file before writing code.**

## Stack

- **React 19** with TypeScript
- **Tailwind CSS v4** for styling
- **Motion for React v12** (formerly Framer Motion) for animations
- Works in both **Claude artifacts** (single-file `.jsx`) and **real project code** (multi-file `.tsx`)

## Workflow — Follow This Every Time

### Step 1: Read the reference

Before writing any animation code, read `references/motion-react.md` for the verified Motion API.

Before writing layout/styling code, consult the Tailwind section below.

### Step 2: Plan before coding

Think through:
- What components are needed?
- What states drive the UI? (hover, open/closed, loading, etc.)
- Which animations serve the UX vs. which are decorative?
- Is this an artifact (single file, no build step) or project code?

### Step 3: Write code following the rules below

### Step 4: Self-check against the hallucination traps

---

## Motion for React — Rules

### Imports

```tsx
// ALWAYS use this import path
import { motion, AnimatePresence } from "motion/react"

// For hooks
import { useAnimate, useMotionValue, useTransform, useScroll, useSpring, useInView, useMotionValueEvent, stagger } from "motion/react"

// For React Server Components (Next.js App Router)
import * as motion from "motion/react-client"
```

**NEVER** use `import { motion } from "framer-motion"` — this is the old package name.

### Core Pattern: Declarative Animation

For most UI work, use the declarative `<motion.div>` pattern:

```tsx
<motion.div
  initial={{ opacity: 0, y: 20 }}
  animate={{ opacity: 1, y: 0 }}
  transition={{ duration: 0.4, ease: "easeOut" }}
/>
```

This is the bread and butter. Use it for enter animations, state changes, and simple interactions.

### When to Use Variants

Use variants when:
- Multiple children need coordinated/staggered animations
- You have named states (open/closed, active/inactive)
- You want to reuse animation targets

```tsx
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: { staggerChildren: 0.08 },
  },
}

const item = {
  hidden: { opacity: 0, y: 20 },
  show: { opacity: 1, y: 0 },
}
```

### When to Use `useAnimate`

Use the imperative `useAnimate` hook when:
- You need sequences (do A, then B, then C)
- You need to trigger animations from events, not state
- You need playback control (pause, speed, scrub)

```tsx
const [scope, animate] = useAnimate()

// Sequence
await animate(scope.current, { opacity: 1 })
await animate("li", { opacity: 1, x: 0 }, { delay: stagger(0.1) })
```

### Spring vs Tween Decision

- **Springs**: Use for physical interactions (drag, tap, layout changes). They feel alive.
- **Tweens**: Use for decorative animations (fade ins, color changes, scroll-linked). They're predictable.

Good spring defaults:
```tsx
// Snappy UI interaction
{ type: "spring", stiffness: 400, damping: 25 }

// Gentle settle
{ type: "spring", stiffness: 200, damping: 20 }

// Bouncy playful
{ type: "spring", stiffness: 300, damping: 15 }
```

### Exit Animations

Always wrap conditionally rendered animated elements in `AnimatePresence`:

```tsx
<AnimatePresence mode="wait">
  {activeTab === "settings" && (
    <motion.div
      key="settings"  // KEY IS REQUIRED
      initial={{ opacity: 0, x: 20 }}
      animate={{ opacity: 1, x: 0 }}
      exit={{ opacity: 0, x: -20 }}
    />
  )}
</AnimatePresence>
```

**Key rules:**
- The direct child of AnimatePresence must have a unique `key`
- `mode="wait"` — waits for exit before entering new child
- `mode="sync"` (default) — enter and exit happen simultaneously
- `mode="popLayout"` — exiting element is popped out of layout flow

### Layout Animations

The simplest way to animate layout changes:

```tsx
<motion.div layout />  // animates position AND size changes
```

For shared element transitions across different components:

```tsx
<motion.div layoutId="card-highlight" />
```

Both elements with the same `layoutId` will animate between each other.

---

## Tailwind CSS — Rules

### In Artifacts (Claude Preview Panel)

Artifacts use Tailwind's pre-defined utility classes only — no compiler. Stick to standard utilities:

```tsx
// SAFE in artifacts
className="flex items-center justify-between p-4 bg-zinc-900 rounded-xl"

// UNSAFE in artifacts — custom values need a compiler
className="bg-[#1a1a2e] p-[13px] grid-cols-[1fr_2fr]"
```

When you need custom values in artifacts, use inline `style` props instead:

```tsx
style={{ backgroundColor: "#1a1a2e", padding: 13 }}
```

### In Real Projects (with build step)

Full Tailwind is available including arbitrary values, custom config, plugins.

### Tailwind + Motion Together

Use Tailwind for static styles, Motion for dynamic/animated styles:

```tsx
<motion.div
  className="rounded-xl bg-zinc-800 p-6 shadow-lg"  // static with Tailwind
  initial={{ opacity: 0, scale: 0.95 }}               // dynamic with Motion
  animate={{ opacity: 1, scale: 1 }}
  whileHover={{ scale: 1.02 }}
/>
```

**Never** try to animate Tailwind classes. Tailwind is for static styling. Motion handles the dynamic part.

---

## React + TypeScript Patterns

### Component Props

```tsx
interface ButtonProps {
  children: React.ReactNode
  variant?: "primary" | "secondary"
  onClick?: () => void
  disabled?: boolean
}

export function Button({ children, variant = "primary", onClick, disabled }: ButtonProps) {
  return (
    <motion.button
      className={variant === "primary" ? "bg-blue-600 text-white" : "bg-zinc-700 text-zinc-100"}
      whileHover={{ scale: 1.02 }}
      whileTap={{ scale: 0.98 }}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </motion.button>
  )
}
```

### Controlled Animation State

```tsx
const [isOpen, setIsOpen] = useState(false)

<motion.div
  animate={isOpen ? "open" : "closed"}
  variants={{
    open: { height: "auto", opacity: 1 },
    closed: { height: 0, opacity: 0 },
  }}
/>
```

### Forwarding Refs for motion.create

```tsx
const Card = React.forwardRef<HTMLDivElement, CardProps>((props, ref) => (
  <div ref={ref} {...props} />
))

const MotionCard = motion.create(Card)
```

---

## Artifact-Specific Rules

When building for Claude's artifact preview panel:

1. **Single file** — everything in one `.jsx` or `.tsx`
2. **Default export** — must have `export default function Component()`
3. **No required props** — provide defaults for everything
4. **Available libraries**: React, lucide-react, recharts, d3, Three.js (r128), lodash, shadcn/ui, Tailwind (core utilities only)
5. **Motion is NOT available** as an import in artifacts — use CSS animations or the Web Animations API instead
6. **No localStorage/sessionStorage** — use React state only
7. **Inline styles** for custom values that Tailwind can't handle

### CSS Animation Fallback for Artifacts

Since Motion isn't available in artifacts, use CSS animations:

```tsx
// Keyframes in a style tag or inline
const fadeIn = {
  animation: "fadeIn 0.4s ease-out forwards",
}

// In JSX
<style>{`
  @keyframes fadeIn {
    from { opacity: 0; transform: translateY(10px); }
    to { opacity: 1; transform: translateY(0); }
  }
  @keyframes slideIn {
    from { opacity: 0; transform: translateX(-20px); }
    to { opacity: 1; transform: translateX(0); }
  }
`}</style>
```

For staggering in artifacts, use `animation-delay`:

```tsx
{items.map((item, i) => (
  <div
    key={item.id}
    style={{
      animation: "fadeIn 0.4s ease-out forwards",
      animationDelay: `${i * 0.08}s`,
      opacity: 0,
    }}
  >
    {item.name}
  </div>
))}
```

---

## Common Hallucination Traps — CHECK EVERY TIME

### ❌ Wrong import path
```tsx
// WRONG
import { motion } from "framer-motion"
// CORRECT
import { motion } from "motion/react"
```

### ❌ `AnimateSharedLayout` does not exist
```tsx
// WRONG — removed years ago
<AnimateSharedLayout>

// CORRECT — use LayoutGroup or just layoutId
import { LayoutGroup } from "motion/react"
```

### ❌ `motion.custom()` does not exist
```tsx
// WRONG
motion.custom(Component)
// CORRECT
motion.create(Component)
```

### ❌ `useAnimation` vs `useAnimate`
```tsx
// OUTDATED (still works but avoid)
const controls = useAnimation()
await controls.start({ opacity: 1 })

// MODERN
const [scope, animate] = useAnimate()
await animate(scope.current, { opacity: 1 })
```

### ❌ Using Motion imports in artifacts
Motion is not bundled in Claude's artifact environment. Use CSS animations for artifacts. Only use Motion imports in real project code.

### ❌ Animating Tailwind classes
```tsx
// WRONG — Tailwind classes aren't animatable
animate={{ className: "bg-red-500" }}

// CORRECT — animate the actual CSS property
animate={{ backgroundColor: "#ef4444" }}
```

### ❌ Missing `key` on AnimatePresence children
```tsx
// WRONG — exit animation won't work
<AnimatePresence>
  {show && <motion.div exit={{ opacity: 0 }} />}
</AnimatePresence>

// CORRECT
<AnimatePresence>
  {show && <motion.div key="unique" exit={{ opacity: 0 }} />}
</AnimatePresence>
```

### ❌ `positionTransition` or `layoutTransition` — these don't exist
```tsx
// WRONG — old API that was removed
<motion.div positionTransition />

// CORRECT
<motion.div layout />
```

---

## Design Principles

When building UI, also read the `frontend-design` skill at `/mnt/skills/public/frontend-design/SKILL.md` for aesthetic guidance. Key points:

- Choose a bold, intentional aesthetic direction
- Use distinctive fonts (not Inter, not Arial, not system defaults)
- Use CSS variables for consistent theming
- Prefer purposeful animations over decoration
- Every animation should serve the UX: guide attention, confirm actions, show relationships, or provide feedback

---

## Quick Reference: What to Use When

| Scenario | Tool |
|----------|------|
| Enter/exit animation | `initial` + `animate` + `exit` with `AnimatePresence` |
| Hover/tap feedback | `whileHover` + `whileTap` |
| Scroll-triggered reveal | `whileInView` + `viewport` |
| Scroll-linked (parallax) | `useScroll` + `useTransform` |
| Staggered list | Variants with `staggerChildren` |
| Shared element transition | `layoutId` |
| Layout change animation | `layout` prop |
| Complex sequence | `useAnimate` |
| Drag interaction | `drag` + `dragConstraints` |
| Spring-connected value | `useSpring` |
| Derived animation value | `useTransform` |
| Artifact animation | CSS `@keyframes` + `animation-delay` |
