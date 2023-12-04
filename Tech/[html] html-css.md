# HMTL-CSS

## HTML Layouts

- `CSS framwork like Bootstrap`
- `CSS Float`

  - The `float` prop have 4 value: left, right, none, inherit.
  - The `clear` property specifies what elements can float beside the cleared element and on which side.

    ```css
    /* Clear floats after the columns (fill the rest with empty) */
    section::after {
      content: '';
      display: table;
      clear: both;
    }
    ```

- `CSS Flexbox`

  - To use Flexox model, define flex container with its props

    ```css
    .flex-container {
      display: flex;

      /* container */

      flex-direction: 'direction of items';
      flex-wrap: 'specific items wrap or not';
      flex-flow: 'shorthand prop for setting both direction and wrap';
      justify-content: 'align items horizontal';
      align-items: 'align items vertical';
      align-content: 'align content';

      /* items */
      order: <number> 'the first doesn\'t mean the first';
      flex-grow: 'specify how much the item will grow relatetive to the rest';
      flex-shrink: 'specify how much the item will shrink relatetive to the rest';
      flex-basis: 'initial length of a flex item'
      flex: <grow> <shrinkl> <basis>;
      align-self: 'align itseft'
    }
    ```

- `CSS Grid`

## Display element

- display: `block` - start on new line, take full width and auto add some space before and after element
- display: `inline` - on current line, take as much width as possible

## Responsive

- `Responsive image`

  > `Notice` that with `width: 100%` the image can be scaled up to be larger than its original size. A better solution, in many cases, will be to use the `max-width` property instead.

- `Responsive text`

  > use `vw` unit (viewport width) to make text follow the size of browser (1vw = 1%)

- `@media`

  ```css
  /* Extra small devices (phones, 600px and down) */
  @media only screen and (max-width: 600px) {
    ...;
  }

  /* Small devices (portrait tablets and large phones, 600px and up) */
  @media only screen and (min-width: 600px) {
    ...;
  }

  /* Medium devices (landscape tablets, 768px and up) */
  @media only screen and (min-width: 768px) {
    ...;
  }

  /* Large devices (laptops/desktops, 992px and up) */
  @media only screen and (min-width: 992px) {
    ...;
  }

  /* Extra large devices (large laptops and desktops, 1200px and up) */
  @media only screen and (min-width: 1200px) {
    ...;
  }

  @media only screen and (orientation: landscape) {
    body {
      background-color: lightblue;
    }
  }
  ```
