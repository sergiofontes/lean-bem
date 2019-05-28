<img src="/images/logo.svg" width="380" height="auto" alt="Lean BEM, by Guava" />

*** 

**Lean BEM** is a cleaner and aesthetically improved modification of the [BEM](https://en.bem.info) method *for CSS*.

It ditches repetition of classes (thus bloated HTML), and all of the double-underscores. Instead, it uses a single `_` for breaking blocks from elements; and modifiers as standalone classes, prepended with `-`. On top of that, it brings back a little bit of cascade.

So, in place of this:
```html
<button class="button button__primary button__primary_disabled button_big">…</button>
```

We'd have this:
```html
<button class="button button_primary -disabled -big">…</button>
```

### What's BEM?

[BEM](https://en.bem.info) (Block, Element, Modifier) is a component-based approach to web development. The idea behind it is to divide the user interface into independent blocks. This makes interface development easy and fast even with a complex UI.

### What about the cascade?

Even though the idea of BEM is to make every block independent from each other, there *should* be some dependence on *base styles*. Think of color palettes, grid settings, modular scale, motion, and, of course, typography. Normally, they’re the most basic ingredients of a UI's recipe; thus, every other receipt may inherit these pieces. In practical terms, these *base styles* will follow a regular block's naming and organization convention.

# Key concepts
## Block

An **independent page component** that can be reused.
- The block name **describes its purpose** (“What is it?” — `menu` or `button`), not its state (“What does it look like?” — `red` or `big`).
- Composed-words separated by a single hyphen `-`. E.g., `block-name`.
- The block shouldn't influence its environment, meaning you **shouldn't set the external geometry**. E.g., `margin` 🚫) or *positioning* for the block.

**Nesting**
- Blocks can be nested in each other.
- You can have any number of nesting levels in the DOM tree.

*Example:*

```html
<!-- `header` block -->
<header class="header">
    <!-- Nested `search` block -->
    <form class="search"></form>
</header>
```

## Element
- A composite **part of a block** that **can't be used separately from it**.
- The element name **describes its purpose** (“What is this?” — `item`, `text`, etc.), not its state (“What type, or what does it look like?” — `red`, `big`, etc.).
- - Composed-words separated by a single hyphen `-`. E.g., `element-name`.
- The structure of an element's full name is `block_element`. The element name is separated from the block name with a *single* underscore (`_`).

**Nesting**
- Elements can be nested inside each other.
- You can have any number of nesting levels.
- An element is always part of a block, not another element. This means that element names *can't* define a hierarchy, such as `block_element-one_element-two` 🚫.

*Example:*

```html
<!-- `search` block -->
<form class="search">
  <!-- `container` element in the `search` block -->
  <div class="search_container">
    <!-- `input` element in the `search` block -->
    <input class="search_input">
    <!-- `button` element in the `search` block -->
    <button class="search_button">Search</button>
  </div>
</form>
```

In the example above, though the elements are nested in the DOM tree, they *must not* be nested in the stylesheets. Like this:

```css
.search {}
.search_container {}
.search_input {}
.search_button {}
```

This allows you to change a block's DOM structure without making changes in the code for each separate element.

## Modifier
- An entity that helps define the **appearance, state, or behavior** of a block or element.
- The modifier name describes its appearance (“What size?” or “Which theme?” and so on — `big` or `dark`), its state (“How is it different from the others?” — `disabled`, `focused`, etc.) and its behavior (“How does it behave?” or “How does it respond to the user?” — such as `switch-theme`).
- A modifier can't be used alone. It should change the appearance, behavior, or state of the entity, not replace it.
- Each modifier is a combined class (used alongside the block or element class).
- The modifier name is preceded by a hyphen `-`, an exceptional *character that visually differentiates* the modifier from the block or element. E.g., `-modifier`.
- Composed-words separated by a single hyphen `-`. E.g., `-modifier-name`.

*Example:*
```html
<!-- `search` block has the `dark` modifier -->
<form class="search -dark">
  <!-- `container` element in the `search` block has the `reverse` modifier -->
  <div class="search_container -reverse">
    <!-- `input` element in the `search` block has the `focused` modifier -->
    <input class="search_input -focused">
    <!-- `button` element in the `search` block -->
    <button class="search_button">Search</button>
  </div>
</form>
```

The CSS of the example above would be:
```css
.search {}
.search.-dark {}
.search_container {}
.search_container.-reverse {}
.search_input {}
.search_input.-focused {}
.search_button {}
```

# CSS concepts
## General recommendations
- Follow @mdo's [Code Guide](http://codeguide.co/#css-syntax) to make code formatting consistent.
- Prefer classes to target an HTML element, excepting the *base styles*.
- Global mixins and settings from *base styles* must cascade through all blocks.
- Avoid too much use of compound words. Instead of `super-long-block-name`, use `block-name`.
- Prefer composition of classes instead of inheritance. This keeps the code uncoupled and flexible.

## Blocks inside blocks
It's totally fine (and expected) to have nested blocks. Since they're functionally independent, they could be freely moved around to compose UI patterns. To accomplish this, styles that are responsible for the external geometry and positioning are set via the parent block.

*Example:*
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

Based on the HTML above, we’d set positioning styles for nested blocks *on the parent*:
```css
.page_container .header { float: left; }
.page_container .footer { float: right; }
```

Not:
```css
.header { float: left; }
```

### What properties to avoid on the main block selector
```css
.avoid {
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

## Base blocks

The *base styles* are small foundational **UI building pieces** that are used throughout all blocks — whether by inheriting or importing. In these base blocks, you may target HTML tags to set default styles and/or reset browser preferences. For example, in `typography`, you may first style all text tags, like `h1`, `p`, `a`, `ul`, etc; after that, you may use classes to target the block's elements.

Base blocks examples are listed. Remove or add more if needed.

| *Base blocks*    | *Description*                                                       |
| ---------------- | ------------------------------------------------------------------- |
| `colors`         | Color pallette                                                      |
| `typography`     | Type scale, families, headers, paragraphs, links, lists, small, etc |
| `grid & spacing` | Grid settings, modular scale & measurements patterns                |
| `motion`         | Motion patterns and effects for interaction states                  |
| `global`         | Resets and global HTML base definitions                             |



## Page blocks

They encompass specific styles for pages and themes. It also can be useful to tie a group of *blocks* together, forming a cohesive layout. On smaller projects, this layer of blocks can be used to tie **styles together without worrying too much about modularity**.


# File structure
Below are two examples of the **Lean BEM** file structure. They're formed by three layers of folders, ordered by importance. Also, inside this repository, you'll find a template for SCSS.

### Default
A default file structure. Set global CSS variables (“CSS Custom Properties”) in the base blocks and re-use them throughout the other blocks (e.g., `colors` palette, `motion` transitions, and so on.)
```
    css/
    ├── base/
    │   ├── colors.css
    │   ├── typography.css
    │   ├── grid_n_spacing.css
    │   ├── motion.css
    │   └── global.css
    ├── blocks/
    │   ├── block_name.css
    │   └── …
    └── pages/
        ├── page_name.css
        └── …
```

### SCSS/Less
A SCSS (or Less) file structure. Set variables, mixins and functions in the `mixins/` folder, and re-use them throughout the other blocks.
```
    scss/
    ├── base/
    │   ├── mixins.scss
    │   ├── typography.scss
    │   ├── grid_n_spacing.scss
    │   ├── motion.scss
    │   └── global.scss
    |   └── mixins/
    │       ├── colors.scss
    │       ├── typography.scss
    │       ├── grid_n_spacing.scss
    │       ├── motion.scss
    │       └── utilities.scss
    ├── blocks/
    │   ├── block_name.scss
    │   └── …
    └── pages/
        ├── page_name.scss
        └── …
```
