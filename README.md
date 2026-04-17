# Single-Axis Scroll Containers explainer

# Background

Scrolling on a single axis is a common pattern on the web. For example, a table may scroll horizontally while the root page scrolls vertically. Historically, CSS `overflow` effectively treats both axes as scrollable values, or neither. This leads to downstream issues for specific features, namely `position: sticky` and DOM scroll APIs.

# Proposal

This proposal extends the functionality of the `overflow` property to support usage of scrollable values with `clip` (for example, `overflow: scroll clip`).

_Note: Even before this proposal, it is possible to specify these combinations, but the `clip` value is automatically converted to `hidden`._

This, among other things, affects the behavior of an element with `position: sticky`. It will now be constrained only if an ancestor element has a scrollable value of overflow on that axis. This gives web developers a concise way, consistent with their existing mental model (`sticky` sticks to a scrollable value), to specify the behavior they need.

It also affects DOM scroll APIs, such as `scrollIntoView()` and `scrollBy()`. An axis set to `overflow: clip` cannot move away from 0. This contrasts with the behavior of `overflow: hidden`, which is not user-interactable but can still be scrolled through script.

> To minimize web compatibility impact, there is no behavior change when only one `overflow` axis is set (for example, `overflow-x: scroll`).

# Examples

## Example 1: Table with labels on top and left

The developer creates a table with labels on the top (for example, the object) and on the left (for example, attributes of an object).

The user is on a mobile device and needs to scroll both vertically and horizontally to see the whole table. It is helpful if the labels for both axes stay visible as they scroll around the table.

With this proposal, the web developer can specify this behavior by setting `overflow: scroll clip` on the table.

## Example 2: Call `scrollIntoView()` within a carousel that is visually clipped

The web developer has a carousel that is visually clipped on the vertical axis (for example, `overflow: scroll hidden`).

The user needs to see a specific item within the carousel. The carousel is off-screen vertically. `scrollIntoView()` is called on a specific item within the carousel.

Since the carousel is set to `overflow: scroll hidden`, the items within the carousel can unexpectedly scroll vertically, because script can still move them. Once this happens, the user has no way to fix it, because the vertical axis is not user-interactable.

With this proposal, the web developer can ensure the visually clipped axis does not move. They can do this by setting `overflow: scroll clip` on the carousel.

# Alternatives considered:

Many alternative solutions have been considered. They include:

## Special casing how `position: sticky` works for the root scroller only

This only solves a subset of the cases we need. It does not generalize to pages that do not use the root scroller for primary scroll interaction, or to any form of nested scroller.

## Directly specifying where a `position: sticky` element sticks with new CSS syntax

This introduces additional cognitive load for web developers, who would need to learn and keep track of new syntax. It also introduces the risk of allowing invalid layouts (for example, two `position: sticky` elements anchored to each other in an infinite loop). This leads to additional complexity, because we would then need to specify what the invalid combinations are and what the behavior should be when they occur.

## Using script to set offsets as the scroller moves

It is possible to emulate single-axis behavior using `scroll` events and script - but this drains battery, is brittle to maintain, and is never perfectly in sync because scrolling is asynchronous.

# Non-goals

Define implications of making scroll containers single-axis for:

- Scroll snap
- Scroll-driven animations
- Block fragmentation when printing
- `background-attachment`

# Accessibility, internationalization, privacy, and security

There are no special a11y, i18n, privacy or security considerations.
