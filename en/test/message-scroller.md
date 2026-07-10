# MESSAGESCROLLER: Message Scroller Acceptance Tests

## Intent

This file states the acceptance tests for the Message Scroller [user](../user/message-scroller.md) and [dev](../dev/message-scroller.md) contracts as Given-When-Then items mapped to GEARS: Given maps to Where/While, When maps to When, and Then maps to the shall clause.

## Accessibility and keyboard

### MESSAGESCROLLER-20

Verifies: [MESSAGESCROLLER-2](../user/message-scroller.md#messagescroller-2), [MESSAGESCROLLER-4](../user/message-scroller.md#messagescroller-4)

Where a message scroller is rendered with content taller than the viewport, when the user presses Tab from the focusable element preceding the scroller, focus shall land on the viewport, and the focused element shall expose `role="region"`, `aria-label="Messages"`, and `tabindex="0"`.

### MESSAGESCROLLER-21

Verifies: [MESSAGESCROLLER-4](../user/message-scroller.md#messagescroller-4)

Where a message scroller is rendered with content taller than the viewport, while focus is on the viewport and the viewport is scrolled to the middle of the transcript, when each key below is pressed, the viewport's scroll offset shall change as paired.

| Key | Expected scroll offset change |
| --- | --- |
| ArrowDown | increases by a small step |
| ArrowUp | decreases by a small step |
| PageDown | increases by approximately one viewport height |
| PageUp | decreases by approximately one viewport height |
| Space | increases by approximately one viewport height |
| Shift+Space | decreases by approximately one viewport height |

### MESSAGESCROLLER-22

Verifies: [MESSAGESCROLLER-4](../user/message-scroller.md#messagescroller-4)

Where a message scroller is rendered with content taller than the viewport, while focus is on the viewport and the viewport is scrolled to the middle of the transcript, when the user presses End, the viewport shall reach its maximum scroll offset (end edge); when the user presses Home, the viewport shall reach scroll offset zero (start edge).

### MESSAGESCROLLER-23

Verifies: [MESSAGESCROLLER-3](../user/message-scroller.md#messagescroller-3)

Where a message scroller is rendered, the content element shall expose `role="log"` and `aria-relevant="additions"`; where the consumer additionally sets `aria-busy` while a reply streams, the content element shall expose `aria-busy="true"` until the stream ends.

## Scrolling behavior

### MESSAGESCROLLER-24

Verifies: [MESSAGESCROLLER-5](../user/message-scroller.md#messagescroller-5)

Where a message scroller mounts with content taller than the viewport and `defaultScrollPosition` set as in the table below, when mounting completes, the viewport shall rest at the paired position.

| `defaultScrollPosition` | Expected resting position |
| --- | --- |
| unset or `"end"` | maximum scroll offset, newest row visible |
| `"start"` | scroll offset zero, oldest row visible |
| `"last-anchor"` | the most recent `scrollAnchor` item's top aligned near the viewport top |

### MESSAGESCROLLER-25

Verifies: [MESSAGESCROLLER-6](../user/message-scroller.md#messagescroller-6)

Where `autoScroll` is enabled, while the viewport rests at the end edge, when a new item is appended, the viewport shall scroll so that the end edge stays visible, and the root and viewport shall expose `data-autoscrolling` during the programmatic scroll and drop it after the scroll settles.

### MESSAGESCROLLER-26

Verifies: [MESSAGESCROLLER-7](../user/message-scroller.md#messagescroller-7), [MESSAGESCROLLER-4](../user/message-scroller.md#messagescroller-4)

Where `autoScroll` is enabled, while the viewport is following appended content, when the reader scrolls up by wheel — and, in a separate run, by PageUp with the viewport focused — and a further item is then appended, the viewport's scroll offset shall not change.

### MESSAGESCROLLER-27

Verifies: [MESSAGESCROLLER-8](../user/message-scroller.md#messagescroller-8)

Where `autoScroll` is enabled, while following is disengaged and the viewport rests away from the end edge, when the reader returns the viewport to the end edge (End key with the viewport focused, or activating the end-direction button) and a new item is then appended, the viewport shall scroll so that the end edge stays visible.

## Scroll buttons

### MESSAGESCROLLER-28

Verifies: [MESSAGESCROLLER-9](../user/message-scroller.md#messagescroller-9)

Where a message scroller renders an end-direction button and the transcript fits inside the viewport, the button shall expose `data-active="false"`, computed opacity 0, computed `pointer-events: none`, and the `inert` attribute, and pressing Tab through the scroller shall skip it.

### MESSAGESCROLLER-29

Verifies: [MESSAGESCROLLER-9](../user/message-scroller.md#messagescroller-9), [MESSAGESCROLLER-10](../user/message-scroller.md#messagescroller-10)

Where the transcript overflows and the viewport rests at the start edge, the end-direction button shall expose `data-active="true"` and the accessible name "Scroll to end"; when the button is clicked, and, in separate runs, when Enter and then Space are pressed while it is focused, the viewport shall scroll to the end edge.
Where a start-direction button is rendered and the viewport rests at the end edge, that button shall expose the accessible name "Scroll to start" and render its arrow icon rotated 180 degrees, and activating it shall scroll the viewport to the start edge.

## Position preservation

### MESSAGESCROLLER-30

Verifies: [MESSAGESCROLLER-11](../user/message-scroller.md#messagescroller-11)

Where `preserveScrollOnPrepend` is left at its default, while the reader views a known row mid-transcript, when twenty older rows are prepended above it, the known row's bounding-box top shall remain unchanged on screen.

### MESSAGESCROLLER-31

Verifies: [MESSAGESCROLLER-12](../user/message-scroller.md#messagescroller-12)

Where `autoScroll` is enabled and new turns are rendered as items with `scrollAnchor` set, while the viewport is following, when a new anchor item is appended and content then streams in below it, the anchor's top shall settle near the viewport top with approximately 64 pixels of the previous row visible above it, and the anchor's on-screen position shall not move while the streamed content grows.

## Implementation surface

### MESSAGESCROLLER-32

Verifies: [MESSAGESCROLLER-13](../dev/message-scroller.md#messagescroller-13), [MESSAGESCROLLER-15](../dev/message-scroller.md#messagescroller-15)

Where a message scroller is rendered from all six exported parts with default props, the rendered elements shall expose `data-slot` values `message-scroller`, `message-scroller-viewport`, `message-scroller-content`, and `message-scroller-item`, and the default button shall expose `data-slot="message-scroller-button"`, `data-direction="end"`, `data-variant="secondary"`, and `data-size="icon-sm"`.

### MESSAGESCROLLER-33

Verifies: [MESSAGESCROLLER-19](../dev/message-scroller.md#messagescroller-19)

Where a probe component under the Provider calls the three exported hooks and the first row is rendered with `scrollAnchor` set, while the transcript overflows and the viewport rests at the start edge, the hooks shall report: `useMessageScrollerScrollable` returns `{ start: false, end: true }`; `useMessageScroller().scrollToMessage("missing-id")` returns `false`; and `useMessageScrollerVisibility` returns the visible rows' `messageId`s in document order with `currentAnchorId` naming the first row's `messageId`.
