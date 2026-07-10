# MESSAGESCROLLER: Message Scroller User Behavior

## Intent

This package pins the user-visible behavioral contract of the official shadcn/ui **Message Scroller** component (`new-york-v4` registry style), a chat-transcript scroller that follows new messages at the live edge, preserves the reading position when history loads, anchors new turns, and floats scroll-to-edge buttons over the viewport.
The upstream source of truth is the registry item at <https://ui.shadcn.com/r/styles/new-york-v4/message-scroller.json>, documented at the styled component page [[1]] and the `@shadcn/react` primitive page [[2]].
The upstream component is distributed under the MIT license, Copyright (c) 2023 shadcn.

## Anatomy

### MESSAGESCROLLER-1

The message scroller shall render the parts in the table below, nested in the order shown.

| Part | Rendered as | Purpose |
| --- | --- | --- |
| Provider | no element of its own | owns scroll state and behavior props for all parts below it |
| Root | a `<div>` frame filling its container | lays out the viewport and floats the scroll buttons |
| Viewport | the vertically scrollable element inside the root | receives scroll input and holds the transcript |
| Content | the transcript container inside the viewport | stacks the rows as a vertical column |
| Item | one wrapper element per transcript row | row boundary used for measuring, anchoring, position preservation, and visibility tracking |
| Button | a floating control over a viewport edge | scrolls the transcript to its start or end |

## Accessibility

### MESSAGESCROLLER-2

The viewport shall be exposed to assistive technology as a `region` landmark with the accessible name "Messages" (`role="region"`, `aria-label="Messages"`).
The viewport shall participate in the tab order (`tabindex="0"`) so that keyboard users can focus it and scroll the transcript.
Where the consumer passes its own `role`, `aria-label`, or `tabIndex`, the viewport shall expose the consumer's value instead of the default.

### MESSAGESCROLLER-3

The content part shall be exposed as an ARIA `log` live region that announces only added rows (`role="log"`, `aria-relevant="additions"`).
Where the consumer sets `aria-busy` while a reply is streaming, the content part shall expose `aria-busy="true"` for the duration of the stream.

## Keyboard

### MESSAGESCROLLER-4

When the user presses Tab, focus shall traverse the scroller in DOM order — the viewport first, then each active button — and inactive buttons shall be skipped (see [MESSAGESCROLLER-9](#messagescroller-9)).
While focus is on the viewport, when one of the keys below is pressed, the viewport shall produce the paired outcome.

| Key | Outcome |
| --- | --- |
| ArrowDown | scrolls the transcript down by a small step |
| ArrowUp | scrolls the transcript up by a small step |
| PageDown | scrolls down by approximately one viewport height |
| PageUp | scrolls up by approximately one viewport height |
| End | scrolls to the end of the transcript |
| Home | scrolls to the start of the transcript |
| Space | scrolls down by approximately one viewport height |
| Shift+Space | scrolls up by approximately one viewport height |

Where `autoScroll` is enabled, while the viewport is following new content, when any key above moves the viewport away from the end edge, following shall disengage as specified in [MESSAGESCROLLER-7](#messagescroller-7).

## Scrolling behavior

### MESSAGESCROLLER-5

When the scroller mounts with content taller than the viewport, the viewport shall open at the position selected by `defaultScrollPosition`, per the table below.

| `defaultScrollPosition` | Opening position |
| --- | --- |
| `"end"` (default) | scrolled to the end edge, newest content visible |
| `"start"` | scrolled to the start edge, oldest content visible |
| `"last-anchor"` | the most recent `scrollAnchor` item aligned near the top of the viewport |

### MESSAGESCROLLER-6

Where `autoScroll` is enabled, while the reader is at the live edge (within `scrollEdgeThreshold` pixels of the end, 8 by default), when new content is appended or streamed into the transcript, the viewport shall scroll so that the end edge stays visible.
While the viewport performs such a programmatic scroll to the latest content, the root and the viewport shall expose the `data-autoscrolling` attribute.

### MESSAGESCROLLER-7

Where `autoScroll` is enabled, while the viewport is following new content, when the reader scrolls away from the end edge by wheel, by touch, by a keyboard scroll key, or by an explicit jump to a message, the viewport shall stop following and shall hold the reader's scroll position while further content is appended.

### MESSAGESCROLLER-8

Where `autoScroll` is enabled, while following is disengaged, when the viewport returns to the live edge — by the reader scrolling to the end, by the End key, or by activating the end-direction button — the viewport shall resume following newly appended content.

## Scroll buttons

### MESSAGESCROLLER-9

While there is no scrollable content in a button's direction, that button shall be inactive: it shall expose `data-active="false"`, be fully transparent and slightly scaled down, ignore pointer events, and carry the `inert` attribute, which removes it from the tab order.
While there is scrollable content in a button's direction, that button shall be active: it shall expose `data-active="true"`, be fully visible floating over its edge (the bottom edge for direction `"end"`, the top edge for direction `"start"`), and be focusable.

### MESSAGESCROLLER-10

While a button is active, when it is activated by a pointer click, or by Enter or Space while focused, the viewport shall scroll to that button's edge — the end of the transcript for direction `"end"` (the default), the start for direction `"start"` — using smooth scrolling by default.
Where no custom children are passed, the button shall render a downward-arrow icon, rotated 180 degrees for direction `"start"`, and shall expose the accessible name "Scroll to end" (direction `"end"`) or "Scroll to start" (direction `"start"`) via visually hidden text.

## Position preservation

### MESSAGESCROLLER-11

Where `preserveScrollOnPrepend` is enabled (the default), while the reader is viewing the transcript, when older rows are prepended above the first visible row, the viewport shall keep the first visible row at the same on-screen position with no visible jump.

### MESSAGESCROLLER-12

Where an item is marked as a `scrollAnchor` (default `false`), while the viewport is following new content, when that item is appended, the viewport shall align the new anchor near the top of the viewport with `scrollPreviousItemPeek` pixels (64 by default) of the previous row visible above it, and shall hold that position while content streams in below the anchor.

## References

[1]: https://ui.shadcn.com/docs/components/radix/message-scroller "shadcn/ui — Message Scroller"
[2]: https://ui.shadcn.com/docs/react/message-scroller "@shadcn/react — Message Scroller API reference"
