# Motion for React — Verified API Reference

> Package: `motion` (v12.x). Previously known as "Framer Motion".
> Import path: `import { motion } from "motion/react"`
> For React Server Components (Next.js): `import * as motion from "motion/react-client"`

## Installation

```bash
npm install motion
```

## Core Concept: The `<motion />` Component

Every HTML and SVG element has a `motion` equivalent:

```tsx
<motion.div />
<motion.button />
<motion.svg />
<motion.path />
<motion.circle />
```

These are identical to normal elements but gain animation props.

---

## Animation Props

### `animate` — Target to animate to on enter and on update

```tsx
<motion.div animate={{ opacity: 1, x: 100 }} />
```

When values change, the component animates automatically.

### `initial` — Starting visual state (before animate runs)

```tsx
<motion.button initial={{ scale: 0 }} animate={{ scale: 1 }} />
```

Set `initial={false}` to skip the enter animation entirely.

### `exit` — Target when removed from DOM (requires AnimatePresence parent)

```tsx
<AnimatePresence>
  {show ? <motion.div key="box" exit={{ opacity: 0 }} /> : null}
</AnimatePresence>
```

### `transition` — Configure how the animation plays

```tsx
<motion.div
  animate={{ scale: 2 }}
  transition={{ duration: 0.5, ease: "easeInOut" }}
/>
```

Can also be nested inside `animate`:

```tsx
<motion.div animate={{ scale: 2, transition: { duration: 2 } }} />
```

---

## Animatable Values

Motion can animate:
- Numbers: `0`, `100`
- Strings with numbers: `"0vh"`, `"10px"`
- Colors: Hex, RGBA, HSLA (freely between types)
- Complex strings: `box-shadow`, `background` with multiple values
- `display: "none"/"block"` and `visibility: "hidden"/"visible"`
- CSS variables
- SVG attributes via `attrX`, `attrY`, `attrScale`

### Independent Transforms

Motion supports independent transform properties via `style` and `animate`:

```tsx
<motion.div
  style={{ x: 0, rotate: 0 }}
  animate={{ x: 100, rotate: 90 }}
/>
```

Available: `x`, `y`, `z`, `rotate`, `rotateX`, `rotateY`, `rotateZ`, `scale`, `scaleX`, `scaleY`, `skew`, `skewX`, `skewY`, `originX`, `originY`

---

## Gesture Props

### Hover

```tsx
<motion.button
  whileHover={{ scale: 1.1 }}
  onHoverStart={() => {}}
  onHoverEnd={() => {}}
/>
```

### Tap

```tsx
<motion.button
  whileTap={{ scale: 0.95 }}
  onTap={() => {}}
  onTapStart={() => {}}
  onTapCancel={() => {}}
/>
```

### Focus

```tsx
<motion.input whileFocus={{ borderColor: "#3b82f6" }} />
```

### Drag

```tsx
<motion.div
  drag          // enables both axes
  drag="x"     // constrain to x-axis
  dragConstraints={{ left: -100, right: 100, top: -50, bottom: 50 }}
  dragElastic={0.2}
  dragMomentum={true}
  whileDrag={{ scale: 1.1 }}
  onDragStart={() => {}}
  onDragEnd={() => {}}
/>
```

### In View (scroll-triggered)

```tsx
<motion.div
  initial={{ opacity: 0 }}
  whileInView={{ opacity: 1 }}
  viewport={{ once: true, amount: 0.5 }}
/>
```

`viewport` options:
- `once: boolean` — only animate first time
- `amount: "some" | "all" | number` — how much must be visible
- `margin: string` — e.g. `"0px -20px 0px 100px"`

---

## Keyframes

Pass an array to any animation value:

```tsx
<motion.div animate={{ x: [0, 100, 50] }} />
```

First value `null` means "start from current":

```tsx
<motion.div animate={{ x: [null, 100, 0] }} />
```

With timing control:

```tsx
<motion.div
  animate={{ x: [0, 100, 0] }}
  transition={{
    duration: 2,
    ease: "easeInOut",
    times: [0, 0.3, 1],  // must match keyframe count
    repeat: Infinity,
    repeatDelay: 1,
  }}
/>
```

---

## Transition Options

### Duration-based (tween)

```tsx
transition={{
  type: "tween",   // default for non-physical values
  duration: 0.3,
  ease: "easeInOut",
  // ease options: "linear", "easeIn", "easeOut", "easeInOut",
  //   "circIn", "circOut", "circInOut",
  //   "backIn", "backOut", "backInOut",
  //   "anticipate",
  //   or [number, number, number, number] cubic bezier
  delay: 0.2,
  repeat: 3,           // or Infinity
  repeatType: "loop",  // "loop" | "reverse" | "mirror"
  repeatDelay: 0.5,
}}
```

### Spring-based

```tsx
transition={{
  type: "spring",
  stiffness: 300,    // default 100
  damping: 20,       // default 10
  mass: 1,           // default 1
  bounce: 0.25,      // 0 = no bounce, 1 = very bouncy (alternative to stiffness/damping)
  visualDuration: 0.5, // overrides duration — time to visually appear to reach target
  restDelta: 0.01,
  restSpeed: 0.01,
}}
```

### Per-value transitions

```tsx
transition={{
  x: { type: "spring", stiffness: 300 },
  opacity: { duration: 0.2 },
}}
```

---

## Variants

Named animation states that propagate through component trees:

```tsx
const variants = {
  hidden: { opacity: 0, y: 20 },
  visible: { opacity: 1, y: 0 },
}

<motion.div
  variants={variants}
  initial="hidden"
  animate="visible"
/>
```

### Orchestration with variants

```tsx
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: {
      delayChildren: 0.3,
      staggerChildren: 0.1,
    },
  },
}

const item = {
  hidden: { opacity: 0, y: 20 },
  show: { opacity: 1, y: 0 },
}

<motion.ul variants={container} initial="hidden" animate="show">
  <motion.li variants={item} />
  <motion.li variants={item} />
  <motion.li variants={item} />
</motion.ul>
```

Orchestration props on parent variant transitions:
- `delayChildren: number` — delay before children start
- `staggerChildren: number` — stagger delay between each child
- `staggerDirection: 1 | -1` — direction of stagger
- `when: "beforeChildren" | "afterChildren"` — sequencing

### `stagger` function (modern API)

```tsx
import { stagger } from "motion/react"

const navVariants = {
  open: {
    transition: {
      delayChildren: stagger(0.07, { startDelay: 0.2 }),
    },
  },
  closed: {
    transition: {
      delayChildren: stagger(0.05, { from: "last" }),
    },
  },
}
```

### Dynamic variants (with `custom` prop)

```tsx
const variants = {
  visible: (custom: number) => ({
    opacity: 1,
    transition: { delay: custom * 0.2 },
  }),
}

<motion.ul animate="visible">
  <motion.li custom={0} variants={variants} />
  <motion.li custom={1} variants={variants} />
  <motion.li custom={2} variants={variants} />
</motion.ul>
```

---

## Components

### `AnimatePresence`

Enables exit animations for children removed from React tree.

```tsx
import { AnimatePresence } from "motion/react"

<AnimatePresence>
  {show && <motion.div key="modal" exit={{ opacity: 0 }} />}
</AnimatePresence>
```

Props:
- `mode: "sync" | "wait" | "popLayout"` — how enters/exits overlap
- `initial: boolean` — animate on first render (default true)
- `onExitComplete: () => void`
- `custom` — pass data to exiting components via `usePresenceData()`
- `propagate: boolean` — nested AnimatePresence exit triggers

Key change trick for slideshows:

```tsx
<AnimatePresence>
  <motion.img
    key={image.src}
    initial={{ x: 300, opacity: 0 }}
    animate={{ x: 0, opacity: 1 }}
    exit={{ x: -300, opacity: 0 }}
  />
</AnimatePresence>
```

### `MotionConfig`

Set default transition and reduced motion for all children:

```tsx
import { motion, MotionConfig } from "motion/react"

<MotionConfig transition={{ duration: 0.5 }} reducedMotion="user">
  {children}
</MotionConfig>
```

### `LayoutGroup`

Group layout animations to avoid conflicts:

```tsx
import { LayoutGroup } from "motion/react"

<LayoutGroup>
  <ComponentA />
  <ComponentB />
</LayoutGroup>
```

### `Reorder`

Drag-to-reorder lists:

```tsx
import { Reorder } from "motion/react"

<Reorder.Group axis="y" values={items} onReorder={setItems}>
  {items.map((item) => (
    <Reorder.Item key={item} value={item}>
      {item}
    </Reorder.Item>
  ))}
</Reorder.Group>
```

---

## Layout Animations

Add `layout` prop to animate layout changes automatically:

```tsx
<motion.div layout />
```

Options:
- `layout={true}` — animate position and size
- `layout="position"` — only position
- `layout="size"` — only size
- `layout="preserve-aspect"` — maintain aspect ratio

### Shared layout animations with `layoutId`

```tsx
// These will animate between each other when one mounts and the other unmounts
<motion.div layoutId="underline" />
```

---

## Hooks

### `useAnimate` — Imperative animation control

```tsx
import { useAnimate } from "motion/react"

function Component() {
  const [scope, animate] = useAnimate()

  const handleClick = async () => {
    await animate(scope.current, { x: 100 })
    await animate("li", { opacity: 1 })  // scoped selector
  }

  return <ul ref={scope}>{children}</ul>
}
```

Sequence syntax:

```tsx
const controls = animate([
  [scope.current, { x: "100%" }],
  ["li", { opacity: 1 }, { delay: stagger(0.1) }],
])
controls.speed = 0.8
controls.stop()
```

### `useMotionValue` — Reactive values without re-renders

```tsx
import { useMotionValue } from "motion/react"

const x = useMotionValue(0)
return <motion.div style={{ x }} />
```

Methods: `.get()`, `.set(value)`, `.getVelocity()`, `.jump(value)`, `.isAnimating()`, `.stop()`, `.on("change", callback)`

### `useTransform` — Derive values from motion values

```tsx
import { useMotionValue, useTransform } from "motion/react"

const x = useMotionValue(0)
const opacity = useTransform(x, [-200, 0, 200], [0, 1, 0])
const color = useTransform(x, [-200, 200], ["#ff0000", "#0000ff"])
```

### `useSpring` — Spring-powered motion value

```tsx
import { useSpring } from "motion/react"

const x = useSpring(0, { stiffness: 300, damping: 20 })
```

### `useScroll` — Scroll-linked values

```tsx
import { useScroll } from "motion/react"

// Page scroll
const { scrollY, scrollYProgress } = useScroll()

// Element scroll
const { scrollYProgress } = useScroll({ target: ref })

// With offset
const { scrollYProgress } = useScroll({
  target: ref,
  offset: ["start end", "end start"],
})
```

### `useInView` — Detect viewport visibility

```tsx
import { useInView } from "motion/react"

const ref = useRef(null)
const isInView = useInView(ref, { once: true })
```

### `useMotionValueEvent` — Listen to motion value events in React

```tsx
import { useMotionValueEvent } from "motion/react"

useMotionValueEvent(scrollY, "change", (latest) => {
  console.log(latest)
})
```

### `useVelocity`

```tsx
const x = useMotionValue(0)
const xVelocity = useVelocity(x)
```

### `useAnimationFrame`

```tsx
import { useAnimationFrame } from "motion/react"

useAnimationFrame((time, delta) => {
  // runs every frame
})
```

### `useReducedMotion`

```tsx
const shouldReduce = useReducedMotion()
```

### `useDragControls`

```tsx
import { useDragControls } from "motion/react"

const controls = useDragControls()
<div onPointerDown={(e) => controls.start(e)} />
<motion.div drag="x" dragControls={controls} />
```

---

## SVG-Specific

### Path drawing

```tsx
<motion.path
  d={pathData}
  initial={{ pathLength: 0 }}
  animate={{ pathLength: 1 }}
/>
```

Special values: `pathLength`, `pathSpacing`, `pathOffset` (all 0-1).

### SVG attribute animation

Use `attrX`, `attrY`, `attrScale` for SVG-specific attributes:

```tsx
<motion.rect attrX={0} animate={{ attrX: 100 }} />
```

### Path morphing

```tsx
<motion.path
  d="M 0,0 l 0,10 l 10,10"
  animate={{ d: "M 0,0 l 10,0 l 10,10" }}
/>
```

Paths must have same number and type of instructions.

---

## motion.create() — Custom components

```tsx
const MotionBox = motion.create(MyComponent)
// MyComponent MUST forward ref to a DOM element
```

⚠️ Do NOT call `motion.create()` inside a render function.

---

## CRITICAL: Common Hallucination Traps

### ❌ WRONG import paths (these are OUTDATED)
```tsx
// WRONG — old Framer Motion import
import { motion } from "framer-motion"

// CORRECT — current Motion import
import { motion } from "motion/react"
```

### ❌ `useAnimation` is NOT the modern API
```tsx
// OUTDATED pattern — still works but prefer useAnimate
import { useAnimation } from "motion/react"
const controls = useAnimation()
controls.start({ opacity: 1 })

// MODERN pattern
import { useAnimate } from "motion/react"
const [scope, animate] = useAnimate()
```

### ❌ `AnimateSharedLayout` does NOT exist anymore
```tsx
// WRONG — removed in Motion 10+
import { AnimateSharedLayout } from "framer-motion"

// CORRECT — just use layoutId, no wrapper needed
// For grouping: use LayoutGroup
import { LayoutGroup } from "motion/react"
```

### ❌ `motion.custom()` does NOT exist
```tsx
// WRONG
const MotionBox = motion.custom(Box)

// CORRECT
const MotionBox = motion.create(Box)
```

### ❌ `usePresence` import has changed
```tsx
// Both of these work from "motion/react":
import { usePresence, useIsPresent } from "motion/react"
```
