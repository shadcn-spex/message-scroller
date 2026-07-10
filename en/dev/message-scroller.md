# MESSAGESCROLLER: Message Scroller Implementation

## Intent

This file states the implementation requirements for the shadcn/ui **Message Scroller** registry item (`new-york-v4` style): exported parts and their slot attributes, class contracts, defaults, dependencies, consumed theme tokens, and composition constraints.
The user-visible contract is in [the user spec](../user/message-scroller.md); the verbatim upstream source is mirrored at [reference/typescript/message-scroller.tsx](../../reference/typescript/message-scroller.tsx).
Primitive props, data attributes, and hooks are documented upstream [[1]].

## Exports and slots

### MESSAGESCROLLER-13

The module shall export exactly these nine names: `MessageScrollerProvider`, `MessageScroller`, `MessageScrollerViewport`, `MessageScrollerContent`, `MessageScrollerItem`, `MessageScrollerButton`, and the re-exported hooks `useMessageScroller`, `useMessageScrollerScrollable`, and `useMessageScrollerVisibility`.
Each rendered part shall stamp the `data-slot` attribute listed in the table below on the element it renders.

| Export | Wraps primitive | `data-slot` |
| --- | --- | --- |
| `MessageScrollerProvider` | `MessageScrollerPrimitive.Provider` | none (renders no element) |
| `MessageScroller` | `MessageScrollerPrimitive.Root` | `message-scroller` |
| `MessageScrollerViewport` | `MessageScrollerPrimitive.Viewport` | `message-scroller-viewport` |
| `MessageScrollerContent` | `MessageScrollerPrimitive.Content` | `message-scroller-content` |
| `MessageScrollerItem` | `MessageScrollerPrimitive.Item` | `message-scroller-item` |
| `MessageScrollerButton` | `MessageScrollerPrimitive.Button` | `message-scroller-button` |

## Class contracts

### MESSAGESCROLLER-14

Each part shall apply the base classes below before the consumer's `className`, merged with `cn` so that consumer classes win on conflict.

| Part | Base classes |
| --- | --- |
| Root | `group/message-scroller relative flex size-full min-h-0 flex-col overflow-hidden` |
| Viewport | `size-full min-h-0 min-w-0 scroll-fade-b scrollbar-thin scrollbar-gutter-stable overflow-y-auto overscroll-contain contain-content data-autoscrolling:scrollbar-none` |
| Content | `flex h-max min-h-full flex-col gap-8` |
| Item | `min-w-0 shrink-0 [contain-intrinsic-size:auto_10rem] [content-visibility:auto]` |

While the viewport carries `data-autoscrolling`, its classes shall hide the scrollbar (`data-autoscrolling:scrollbar-none`).

### MESSAGESCROLLER-15

`MessageScrollerButton` shall default the props in the table below, mirror each as a data attribute on the rendered element, and, where no `render` prop is passed, render through the registry Button as `<Button variant={variant} size={size} />`.

| Prop | Default | Mirrored data attribute |
| --- | --- | --- |
| `direction` | `"end"` | `data-direction` |
| `variant` | `"secondary"` | `data-variant` |
| `size` | `"icon-sm"` | `data-size` |

The button shall apply the state classes below.

| State | Classes |
| --- | --- |
| base | `absolute inset-s-1/2 -translate-x-1/2 border-border bg-background text-foreground transition-[translate,scale,opacity] duration-200 hover:bg-muted hover:text-foreground rtl:translate-x-1/2` |
| `data-[active=false]` | `pointer-events-none scale-95 opacity-0 duration-400 ease-[cubic-bezier(0.7,0,0.84,0)]` |
| `data-[active=true]` | `translate-y-0 scale-100 opacity-100 ease-[cubic-bezier(0.23,1,0.32,1)]` |
| `data-[direction=end]` | `bottom-4`; while also inactive, `translate-y-full` |
| `data-[direction=start]` | `top-4 [&_svg]:rotate-180`; while also inactive, `-translate-y-full` |

Where no `children` are passed, the button shall render `<ArrowDownIcon />` followed by an `sr-only` span reading "Scroll to end" for direction `"end"` or "Scroll to start" for direction `"start"`.

## Dependencies

### MESSAGESCROLLER-16

The registry item shall declare the npm dependency `@shadcn/react` and the registry dependency `button` (`registryDependencies: ["button"]`).
The module shall import the primitive parts and hooks from `@shadcn/react/message-scroller`, `ArrowDownIcon` from `lucide-react`, `cn` from `@/lib/utils`, and `Button` from the registry `button` item — the latter three provided by the shadcn base setup.

## Theme tokens

### MESSAGESCROLLER-17

The component's own classes shall consume only the semantic theme tokens in the table below.

| Token | Consumed by |
| --- | --- |
| `--border` | `border-border` on the button |
| `--background` | `bg-background` on the button |
| `--foreground` | `text-foreground` and `hover:text-foreground` on the button |
| `--muted` | `hover:bg-muted` on the button |

Additional tokens (`--secondary`, `--secondary-foreground`, `--ring`) shall enter only through the registry `button` dependency's `secondary` variant and focus styles.

## Composition

### MESSAGESCROLLER-18

The Root, Viewport, Content, Item, and Button parts shall function only when nested under a Provider, which owns the scroll state they read.
Every direct child of Content shall be wrapped in an Item so that the scroller can measure, anchor, preserve, and track it.
`MessageScrollerItem` shall default `scrollAnchor` to `false`.
`MessageScrollerButton` shall accept a `render` prop that substitutes the rendered element while keeping the primitive's scroll behavior.

## Hooks

### MESSAGESCROLLER-19

Where called under a Provider, each hook shall return the values in the table below.

| Hook | Returns |
| --- | --- |
| `useMessageScroller` | `scrollToMessage(messageId, options?)`, `scrollToEnd(options?)`, `scrollToStart(options?)`; each command returns `false` when its target is unavailable |
| `useMessageScrollerScrollable` | `{ start: boolean, end: boolean }` — whether scrollable content exists toward each edge |
| `useMessageScrollerVisibility` | `{ currentAnchorId: string \| null, visibleMessageIds: string[] }` with ids in document order |

## References

[1]: https://ui.shadcn.com/docs/react/message-scroller "@shadcn/react — Message Scroller API reference"
