<p align="center">
  <img src="/images/logo.svg" width="450" height="auto" alt="Lean BEM, by Guava" align="center" />
</p>
<p align="center">
  <em>It’s BEM. But lean.</em>
</p>

***

[BEM](https://en.bem.info/methodology/) (Block, Element, Modifier) is a component-based approach to web development. It's a methodology that helps you to name, understand & organize your stylesheets.

The main idea is to slice the interface into **independent blocks**, which are formed by elements. Each block and element hold a unique naming scheme. This modular approach makes for faster development and easier maintenance because the issues of inheritance and over-specificity are avoided by design.

And what means each of these BEM words? [Block](#block) stands for an independent component (or module), no matter its complexitiy. [Element](#element) is a constituent of the block, and can't be used separately. And finally, [modifier](#modifier) conveys appearance, state or behavior of both block and element, and just like the latter, can't be used separately.

<img src="/images/intro.svg" width="100%" height="auto" alt="A figure with a dissected block, showcasing all of its elements and modifiers" />

# What's Lean BEM
It's a readability-driven, alternate naming convention to the [classic BEM](https://en.bem.info/methodology/). Composed-words are separated by a single hyphen `-`; [blocks](#block) are separated from [elements](#element) by a single underline `_`; and [modifiers](#modifier) are standalone classes that only work within the parent block. That’s it.

So, in place of this:
```html
<!-- Yandex's BEM—https://en.bem.info/methodology/ -->
<button class="button button__primary button__primary_disabled button_big">…</button>

<!-- Non-canonical BEM—http://getbem.com -->
<button class="button button__primary button__primary--disabled button--big">…</button>
```
We'd have this:
```html
<button class="button button_primary -disabled -big">…</button>
```

Lean BEM (and BEM as well) helps to...
- Develop truly modular stylesheets;
- Move layout fragments around within a project safely;
- Create stable, predictable and clear code;
- Reduce the project debugging time.

By ensuring...
- Reusable components;
- Independent styles;
- Tame of cascade and inheritance hell;
- Use of an understandable naming scheme.

### Why lean?
1. It's more readable—the classes' names are shorter, less uglier and without (much) repetition.
2. It denotes modifier classes as composable, which can be added or removed without much prejudice to the block.

# Key concepts
## Block

<p>
<img src="/images/block.svg" width="155" height="auto" alt="Example of a block" align="right" />
<h3>An independent page component that can be reused.</h3>
</p>

- The block name **describes its purpose** (“What is it?”—`button` or `icon`)
  - It *doesn't* describe its state (“What does it look like?”—`red` or `big`). 🚫
- Composed-words separated by a single hyphen `-`. Eg., `.block-name`.
- The block **shouldn't influence its environment**, meaning you shouldn't set the external geometry or positioning on it.
- [Blocks can be nested](#blocks-inside-blocks) inside each other.

```html
<!-- `button` block -->
<button class="button">…</button>
```
```css
/* `button` block */
.button {…}
```

## Element

<p>
<img src="/images/element.svg" width="466" height="auto" alt="Example of a element" align="right" />
<h3>A composite part of a block that can't be used separately from it.</h3>
</p>

- The element name **describes its purpose** (“What is this?”—`item`, `text`, etc.), not its state (“What type, or what does it look like?”—`red`, `big`, etc.).
- Composed-words separated by a single hyphen `-`. Eg., `element-name`.
- The structure of an element's full name is `block_element`. The element name is separated from the block name with a *single* underscore (`_`).
- Elements can be nested inside each other. You can have any number of nesting levels.
- An element is always part of a block, not another element.
  - This means that element names *can't* define a hierarchy, such as `block_element-one_element-two`. 🚫

```html
<!-- `button` block -->
<button class="button button_secondary">
  <!-- `label` element of the `button` block -->
  <span class="button_label">Download</span>
  <!-- `icon` element of the `button` block -->
  <span class="icon icon_download"></span>
</button>
```
```css
/* `button` block */
.button {…}
/* `secondary` element of the `button` block */
.button_secondary {…}
/* `label` element of the `button` block */
.button_label {…}
```
```css
/* `download` element of the `icon` block */
.icon_download {…}
```
In the example above, although the elements are nested in the DOM tree, they *must not* be nested in the stylesheets. Keeping the CSS specificity low is great for maintenance, and makes overwriting a simple task.

## Modifier

<p>
<img src="/images/modifiers.svg" width="339" height="auto" alt="Example of a modifiers" align="right" />
<h3>An entity that helps define the appearance, state, or behavior of a block or a element.</h3>
</p>

- The modifier name describes its appearance (“What size?” or “Which theme?” and so on—`-big` or `-dark`), its state (“How is it different from the others?”—`-disabled`, `-focused`, etc.) and its behavior (“How does it behave?” or “How does it respond to the user?”—such as `-switch-theme`).
- **A modifier can't be used alone. It should change the appearance, behavior, or state of the entity, not replace it**.
- Each modifier is a combined class (used alongside the block or element class).
- The modifier name is preceded by a hyphen `-`, an exceptional *character that visually differentiates* the modifier from the block or element. Eg., `-modifier`.
- Composed-words separated by a single hyphen `-`. Eg., `-modifier-name`.
- When possible, combine a modifier class with its parent one. Eg., `.button.-big {…}`.
  - Or combine them with an element. Eg., `.button_primary.-disabled {…}`.
  - Don't nest modifier classes, or you can end up missing the target. Eg., `.button .-big {…}`. 🚫

```html
<!-- `button` block with `-big` modifier -->
<button class="button button_secondary -big">
  <!-- `label` element of the `button` block -->
  <span class="button_label">Download</span>
  <!-- `download` element of the `icon` block with `-big` modifier -->
  <span class="icon icon_download -big"></span>
</button>
```
```css
/* `button` block with `-big` modifier */
.button.-big {…}
```
```css
/* `icon` block with `-big` modifier */
.icon.-big {…}
```

# CSS concepts
## General recommendations
- Target classes instead of an HTML tag. Don't target an ID or an attribute.
- Rely in a code formatting standard to make them consistent.
  - Lean BEM suggests [@mdo's Code Guide](http://codeguide.co/#css-syntax).
- Avoid too much use of compound words. Instead of `super-long-block-name`, use `block-name`. Preferably, `block`.
- Avoid much class nesting in CSS.
  - Try to stick with two levels of depth. Eg., `.button .icon_download {…}`.
- Prefer composition of classes instead of inheritance. This keeps the code uncoupled and flexible.

## Blocks inside blocks
It's totally fine (and expected) to have nested blocks. Since blocks are functionally independent, they could be freely moved around to compose UI patterns. To accomplish this, styles that are responsible for the external geometry and positioning are set via the parent block.

In other words, **you shall not set external geometry/positioning in the main block selector**.

```html
<!-- `page` block -->
<body class="page">
  <!-- `container` element in the `page` block -->
  <div class="page_container">
    <!-- `header` block -->
    <header class="header">…</header>
    <!-- `footer` block -->
    <footer class="footer">…</footer>
  </div>
</body>
```
```css
/* `page_container` sets positioning of the `header` block */
.page_container .header { float: left; }
/* `page_container` sets positioning of the `footer` block */
.page_container .footer { float: right; }
```

*Please don't* 🚫
```css
/* Never set external geometry/positioning on the main block selector */
.header { float: left; }
```

### Properties to avoid on the main block selector
```css
.block {
  position: fixed | absolute | sticky;
  top: any;
  right: any;
  bottom: any;
  left: any;
  margin: any;
  float: any;
  clear: any;
  order: any;
  flex: any;
  align-self: any;
  grid-column: any;
  grid-row: any;
  grid-area: any;
  justify-self: any;
  align-self: any;
  box-sizing: content-box | padding-box | inherit;
}
```

# Further reading
- [BEM For Beginners: Why You Need BEM](https://www.smashingmagazine.com/2018/06/bem-for-beginners/#the-basics-of-bem)
- [BEM Methodology](https://en.bem.info/methodology/)
