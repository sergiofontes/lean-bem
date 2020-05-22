<img src="/images/logo.svg" width="450" height="auto" alt="Lean BEM, by Guava" />

***

It’s BEM. But cleaner and prettier.

# What's Lean BEM
It's an alternate naming convention to the classic BEM method. And it's simple: composed-words are separated by a single hyphen `-`; blocks are separated from elements by a single underline `_`; and modifiers are standalone classes that only work within the parent block. That’s it.

So, in place of this:
```html
<!-- Yandex's BEM — https://en.bem.info/methodology/ -->
<button class="button button__primary button__primary_disabled button_big">…</button>

<!-- Non-canonical BEM — http://getbem.com -->
<button class="button button__primary button__primary--disabled button--big">…</button>
```
We'd have this:
```html
<button class="button button_primary -disabled -big">…</button>
```

#### Why?
1. It's more readable—the classes' names are shorter, (less) ugly and without (much) repetition.
2. It denotes modifier classes as composables, which can be added or removed without much prejudice to the block.

But there's more than meets the eye. Lean BEM methodology brings some old concepts back to the spotlight.

#### Other differences from canonical BEM
- It's ok to rely a little bit on cascading.
  - Lean BEM has global blocks[1] to share things like variables, color palette and typography with other components.
  - CSS resets are still important. As said above, we shouldn't fear (so much) the cascading.
- It's necessary to combine selectors when using modifiers—it cannot be used without an element class.[2]

[1] Lean BEM divides the blocks into three subcategories: base (or global), regular, and pages. The base blocks are global styles that cascade throughout other blocks; the regular blocks are the UI components; and pages blocks are a cluster of regular blocks within a place or theme.
[2] For instance:
```css
/* Wrong; could possibly affect other components */
.-big {}
/* Correct; only affect the `button` block */
.button.-big {}
```

But let's take a step back and review what BEM is...

# What's BEM?
[BEM](https://en.bem.info/methodology/) (Block, Element, Modifier) is a component-based approach to web development. The idea is to divide the user interface into **independent blocks**, which are formed by elements and may have some modifiers. This modular approach makes for faster development and easier maintenance.

But what means each of these BEM words? Block stands for an independent component, no matter its complexitiy. Element is a part of the block, and can't be used separately. Modifier conveys appearance, state or behavior.

## Block
<img src="/images/block.svg" width="155" height="auto" alt="A block example" />

An **independent page component** that can be **reused**.
- The block name **describes its purpose** (“What is it?” — `button` or `icon`)
  - It *doesn't* describe its state (“What does it look like?” — `red` or `big`) 🚫.
- Composed-words separated by a single hyphen `-`. E.g., `.block-name`.
- The block **shouldn't influence its environment**, meaning you shouldn't set the external geometry or positioning on it.

#### Nesting
- [Blocks can be nested](#blocks-inside-blocks) in each other.
- You can have any number of nesting levels in the DOM tree.

*Example:*
```html
<!-- `button` block -->
<button class="button">…</button>
```
```css
/* `button` block */
.button {}
```

## Element
<img src="/images/element.svg" width="500" height="auto" alt="A element example" />

- A composite **part of a block that can't be used separately from it**.
- The element name **describes its purpose** (“What is this?” — `item`, `text`, etc.), not its state (“What type, or what does it look like?” — `red`, `big`, etc.).
- Composed-words separated by a single hyphen `-`. E.g., `element-name`.
- The structure of an element's full name is `block_element`. The element name is separated from the block name with a *single* underscore (`_`).

#### Nesting
- Elements can be nested inside each other.
- You can have any number of nesting levels.
- An element is always part of a block, not another element.
  - This means that element names *can't* define a hierarchy, such as `block_element-element-one_element-two` 🚫.

*Example:*
```html
<!-- `button` block -->
<button class="button button_secondary">
  <!-- `label` element of the `button` block -->
  <span class="button_label">Download</span>
  <!-- `icon` element of the `button` block -->
  <span class="icon_download"></span>
</button>
```
```css
/* `button` block */
.button {}
/* `secondary` element of the `button` block */
.button_secondary {}
/* `label` element of the `button` block */
.button_label {}
```
```css
/* `search` element of the `icon` block */
.icon_download {}
```
In the example above, although the elements are nested in the DOM tree, they *must not* be nested in the stylesheets. Keeping the CSS specificity low is great for maintenance, and makes overwriting a simple task.

## Modifier
<img src="/images/modifier.svg" width="339" height="auto" alt="A modifier example" />

- An entity that helps define the **appearance, state, or behavior of a block or element**.
- The modifier name describes its appearance (“What size?” or “Which theme?” and so on — `big` or `dark`), its state (“How is it different from the others?” — `disabled`, `focused`, etc.) and its behavior (“How does it behave?” or “How does it respond to the user?” — such as `switch-theme`).
- A modifier can't be used alone. It should change the appearance, behavior, or state of the entity, not replace it.
- Each modifier is a combined class (used alongside the block or element class).
- The modifier name is preceded by a hyphen `-`, an exceptional *character that visually differentiates* the modifier from the block or element. E.g., `-modifier`.
- Composed-words separated by a single hyphen `-`. E.g., `-modifier-name`.

*Example:*
```html
<!-- `button` block with `-big` modifier -->
<button class="button button_secondary -big">
  <!-- `label` element of the `button` block -->
  <span class="button_label">Download</span>
  <!-- `download` element of the `icon` block with `-big` modifier -->
  <span class="icon_download -big"></span>
</button>
```
```css
/* `button` block with `-big` modifier */
.button.-big {}
```
```css
/* `icon` block with `-big` modifier */
.icon_download.-big {}
```

# CSS concepts
## General recommendations
- Prefer classes to target an element instead of an HTML tag.
- Rely in a code formatting standard to make them consistent.
  - Lean BEM suggests @mdo's [Code Guide](http://codeguide.co/#css-syntax).
- Avoid too much use of compound words. Instead of `super-long-block-name`, use `block-name`. Preferably, `block`.
- Prefer composition of classes instead of inheritance. This keeps the code uncoupled and flexible.

## Blocks inside blocks
It's totally fine (and expected) to have nested blocks. Since they're functionally independent, they could be freely moved around to compose UI patterns. To accomplish this, styles that are responsible for the external geometry and positioning are set via the parent block.

In other words, **you shall not set external geometry/positioning in the main block selector**.

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
```css
/* `page_container` sets positioning of the `header` block */
.page_container .header { float: left; }
/* `page_container` sets positioning of the `footer` block */
.page_container .footer { float: right; }
```

*Wrong example:* 🚫
```css
/* Never set external geometry/positioning on the main block selector */
.header { float: left; }
```

#### Properties to avoid on the main block selector
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

## Base blocks
The *base blocks* are small foundational **UI building pieces** that are needed by the other blocks. Even though the overall idea is to make every block independent from each other, there *should* be some dependence on some elemental styles of a design system. For example, a typographic scale may define the most basic measurement unit of a system (`rem`); or a brand's color palette may be needed to tint elements; and so on.

In practical terms, these *base blocks* will follow a regular block's naming and organization convention, except that is expected that you target HTML selectors before you use classes.

Some *base blocks* examples.

| *Base blocks* | *Base block selector*    | *Description*                                                       |
| ------------- | ------------------------ | ------------------------------------------------------------------- |
| `colors`      | `.color`                 | Color pallette                                                      |
| `typography`  | `.typography` or `.typo` | Type scale, families, headers, paragraphs, links, lists, small, etc |
| `motion`      | `.motion`                | Motion patterns and effects for interaction states                  |
| `global`      | `.global`                | Resets and global HTML base definitions                             |

## Page blocks
They encompass specific styles for pages and themes. It also can be useful to tie a group of *blocks* together, forming a cohesive layout. On smaller projects, this layer can be used to tie **styles together without worrying too much about modularity**. Just add a `page-` prefix before the page name.

For example, a login page would have a `page-login` stylesheet that would set the styles and link together the `inputs`, `buttons`, and `imagery` blocks (alongside the [base blocks](#base-blocks), of course).

# File structure
Below are two examples of a Lean BEM file structure. They're formed by three layers of folders, ordered by importance: `base`, `blocks` and `pages`. Also, inside this repository, you'll find a template for SCSS.

#### Default
A default file structure. Set global CSS variables (“CSS Custom Properties”) in the [base blocks](#base-blocks) and re-use them throughout the other blocks.
```
    css/
    ├── base/
    │   ├── colors.css
    │   ├── typography.css
    │   ├── motion.css
    │   └── global.css
    ├── blocks/
    │   ├── block-name.css
    │   └── …
    └── pages/
        ├── page-name.css
        └── …
```

#### SCSS/Less
A SCSS (or Less) file structure. Set variables, mixins and functions in the `mixins/` folder, and re-use them throughout the other blocks.
```
    scss/
    ├── base/
    │   ├── mixins.scss
    │   ├── typography.scss
    │   ├── motion.scss
    │   ├── global.scss
    │   └── mixins/
    │       ├── colors.scss
    │       ├── typography.scss
    │       ├── grid.scss
    │       ├── motion.scss
    │       └── utilities.scss
    ├── blocks/
    │   ├── block-name.scss
    │   └── …
    └── pages/
        ├── page-name.scss
        └── …
```

# Further reading
- [BEM For Beginners: Why You Need BEM](https://www.smashingmagazine.com/2018/06/bem-for-beginners/#the-basics-of-bem)
- [BEM Methodology](https://en.bem.info/methodology/)
