---
name: lean-bem
description: Naming convention and CSS architecture rules for writing stylesheets and HTML class names with Lean BEM. Use when writing, reviewing, or refactoring CSS/HTML: naming classes, creating components, or organizing stylesheets in a project that follows Lean BEM (or BEM).
---

# Lean BEM

Lean BEM is a readability-driven, alternate naming convention to classic BEM. The interface is sliced into **independent blocks**, which are formed by **elements**; **modifiers** adjust the appearance, state, or behavior of either. Apply these rules whenever you write or review HTML class names and CSS.

## Naming scheme

| Entity | Pattern | Example |
|---|---|---|
| Block | `block` | `.button`, `.icon` |
| Element | `block_element` | `.button_label`, `.icon_download` |
| Modifier | `-modifier` | `.-big`, `.-disabled` |

Each separator has exactly one meaning:

- A **single hyphen** (`-`) joins composed words: block `date-picker`, element `date-picker_nav-button`, modifier `-switch-theme`.
- A **single underscore** (`_`) separates the block name from the element name; it has no other use.
- A **leading hyphen** (`-`) marks a modifier: an extra class added next to the block or element class it modifies, never used by itself.

```html
<!-- Classic BEM -->
<button class="button button__primary button__primary_disabled button_big">…</button>

<!-- Lean BEM -->
<button class="button button_primary -disabled -big">…</button>
```

In the Lean BEM line, `button` is the block, `button_primary` is an element (a variant of the button), and `-disabled` and `-big` are modifiers. Watch the underscore when translating from classic BEM: there, a single underscore marks a *modifier* (`button_big`); in Lean BEM it marks an *element*, and modifiers are the leading-hyphen classes.

## Block

An independent, reusable component.

- Name it by **purpose**, answering “What is it?” (`button`, `icon`), never by state or appearance (`red`, `big` 🚫).
- Prefer short names: `block` over `long-block` over `super-long-block`.
- Blocks can be nested inside other blocks (see [Blocks inside blocks](#blocks-inside-blocks)).
- A block **must not influence its environment**: never set external geometry or positioning on the main block selector. The parent positions it instead.

```html
<!-- `button` block -->
<button class="button">…</button>
```
```css
.button {…}
```

## Element

A composite part of a block that can't be used separately from it.

- Name it by **purpose**, answering “What is this?” (`item`, `text`, `label`), not by state or appearance.
- The full class name is `block_element`, with a *single* underscore: `button_label` is the `label` element of the `button` block.
- The class name always starts with the block name, no matter how deep the element's tag sits in the DOM. Elements belong to the block, never to another element, so names never chain: even if `element-two`'s tag is nested inside `element-one`'s, its class is `block_element-two`, not `block_element-one_element-two` 🚫.
- Style every element with a flat, single-class selector. Elements nested in the DOM **must not** be nested in the stylesheet: `.button_label {…}` ✅, `.button .button_label {…}` 🚫. Flat selectors keep specificity low and overrides simple.
- An element can sit on the same tag as its block when it names a variant of it (`class="button button_secondary"`); see [Element or modifier?](#element-or-modifier).

```html
<!-- `button` block with its `secondary` variant element -->
<button class="button button_secondary">
  <!-- `label` element of the `button` block -->
  <span class="button_label">Download</span>
  <!-- nested `icon` block with its `download` element -->
  <span class="icon icon_download"></span>
</button>
```
```css
.button {…}
.button_secondary {…}
.button_label {…}
.icon_download {…}
```

## Modifier

A class that defines the appearance, state, or behavior of a block or element.

- It describes appearance, such as `-big` or `-dark` (“What size? Which theme?”); state, such as `-disabled` or `-focused` (“How is it different from the others?”); or behavior, such as `-switch-theme` (“How does it respond to the user?”).
- **It never stands alone**: a modifier changes an entity, it doesn't replace it. `class="button -big"` ✅, `class="-big"` 🚫.
- In CSS, chain the modifier to the class it modifies, with no space between them:
  - `.button.-big {…}` ✅ (block with modifier).
  - `.button_primary.-disabled {…}` ✅ (element with modifier).
  - `.button .-big {…}` 🚫: the space turns it into a descendant selector, which styles *any* inner entity carrying `-big` (like the icon below) instead of the button itself.
- Modifier names are generic on purpose, so they can be reused across blocks with different styles (`.button.-big`, `.icon.-big`). That's also why each rule must be scoped to a parent class: a bare `.-big {…}` would leak into every block that uses the name.

```html
<!-- `button` block with the `-big` modifier -->
<button class="button button_secondary -big">
  <span class="button_label">Download</span>
  <!-- the nested `icon` block carries its own `-big` -->
  <span class="icon icon_download -big"></span>
</button>
```
```css
.button.-big {…}
.icon.-big {…}
```

### Element or modifier?

Both can qualify a block, so choose by what the class expresses:

- **Element**: a named variant that defines what the entity *is* (“What is this?”), like `button_primary` and `button_secondary`.
- **Modifier**: a composable adjustment that can be added or removed without changing what the entity is, like `-big`, `-disabled`, and `-dark`.

A big, disabled primary button is `class="button button_primary -big -disabled"`.

## CSS rules

- Selectors target classes only, never tags, IDs, or attributes: `.button {…}` ✅; `button {…}`, `#button {…}`, `[type="submit"] {…}` 🚫.
- Default to flat, single-class selectors. Reserve descendant selectors for a parent styling a nested block (typically to position it), and stop at two levels: `.page_container .header {…}` ✅, `.button .icon_download {…}` ✅, anything deeper 🚫.
- Prefer composition over inheritance: build variations by combining block, element, and modifier classes in the markup, not by extending or duplicating rules in the stylesheet. Composition keeps code uncoupled and flexible.
- Follow a consistent code formatting standard ([@mdo's Code Guide](http://codeguide.co/#css-syntax) is the suggested one).

## Blocks inside blocks

Nesting blocks is fine and expected. Blocks are functionally independent, so they can be moved around freely to compose UI patterns, which only works if a block never positions itself. **The parent sets the positioning of the blocks nested inside it.**

```html
<!-- `page` block -->
<body class="page">
  <!-- `container` element of the `page` block -->
  <div class="page_container">
    <!-- nested `header` block -->
    <header class="header">…</header>
    <!-- nested `footer` block -->
    <footer class="footer">…</footer>
  </div>
</body>
```
```css
/* The parent's element positions each nested block ✅ */
.page_container .header { float: left; }
.page_container .footer { float: right; }
```
```css
/* Never set positioning on the main block selector 🚫 */
.header { float: left; }
```

If `.header` floated itself, it would carry that positioning into every context where it's reused. Owned by `.page_container`, the positioning applies only there.

## Quick checklist

When writing or reviewing code, verify:

1. Class names describe purpose (`button`, `button_label`), not state or appearance (`red-button` 🚫).
2. Element classes have exactly one underscore, block name first (`block_element`); elements never chain (`block_element-one_element-two` 🚫).
3. Modifiers start with `-`, never appear alone in HTML, and are chained without a space in CSS (`.block.-modifier`, never `.block .-modifier`).
4. No positioning on a main block selector; the parent positions nested blocks.
5. Selectors target classes only; elements are styled flat; descendant selectors go at most two levels, reserved for a parent styling a nested block.

## Further reading

- [Lean BEM repository](https://github.com/sergiofontes/lean-bem)
- [BEM Methodology](https://en.bem.info/methodology/)
- [BEM For Beginners: Why You Need BEM](https://www.smashingmagazine.com/2018/06/bem-for-beginners/#the-basics-of-bem)
