# Utilities
Class or placeholder selectors that produces a single `value: property`.
```
.u-color-black { color: black; }
.u-flex { display: flex; }
.u-pos-absolute { position: absolute; }
```

_In some circumstances_ - like `mh` (`margin-left`, `margin-right`), a utility produces two or more properties.
```
.u-mh-auto {
  margin-left: auto;
  margin-right: auto;
}
```

Utilities are "created" by passing a sass map (hence the double brackets) into the make-utility mixin:
```
@include make-utility((...));
```
The items in the map can include an __alias__ to determine the name of the utility, a __class__ flag which creates the utility as a class which is always included in the transpiled CSS and can be used in the HTML (rather than just a placeholder which _cannot_ be used in the HTML, and is only in the transpiled CSS if it gets extended), and a __set of CSS properties__.
```
@include make-utility((
  alias: 'mh-auto',
  class: true,
  margin-left: auto,
  margin-right: auto
));
```
If an __alias__ is not included in the sass map, the first CSS property and value is used as the name of the utility, e.g.
```
@include make-utility((
  height: 1px
));
```
... can be used with:
```
@extend %u-height-1px;
```

See examples in the pre-existing utilties: [`/utilties/*.scss`](https://github.com/wearelighthouse/stemCSS/tree/master/utilities).


# Objects
Layouts objects - grids, containers, and helpful sets of classes like aspect-ratio etc.  
Never *visual* - you can't picture an object because it has no colours. If it has _colours_ - it's _probably_ a component.  
Objects typically @extend utilities.
```
.o-container {
  @extend .u-mh-auto;
  width: 90vw;
}
```

# Components
*Visual components* in your design system. `Header, Button, Footer, Hero` etc.  
Follow the *BEM* methodology for their children _where appropriate_.  
```
.c-button {
  @extend .u-flex;
  @extend .u-bgcolor-black;
  @extend .u-color-white;

  &__icon {
    @extend .u-pos-absolute;
    @extend .u-left-50pc;
    transform: translateX(-50%);
  }
}
```

# Breakpoints

### Defining Breakpoints
These are defined in `settings/_breakpoints`, which includes:

General breakpoints as sass variables - can simply be used anywhere you need them!
```
$breakpoint-minimum: 320px;
$breakpoint-large: 1400px;
```

Breakpoints sass map (get merged with the following map, sans any special generation)
```
$global-breakpoints: (
  landscape: 'screen and (orientation: landscape)',
  portrait: 'screen and (orientation: portrait)'
);
```

Breakpoints sass map with automatic --from and --upto generation
```
$global-breakpoints: map-merge($global-breakpoints, make-width-breakpoints((
  small: 400px,
  medium: 800px,
  large: 1200px,
)));
```

### Using Breakpoints

##### iotaCSS style
```
@include breakpoint(from-medium) {
  color: red;
}
```

##### stemCSS style
```
@extend %u-color-red--from-medium;
```
The main advantage of using "stemCSS style" breakpoints are that they are extending placeholders, rather than requiring additional individual CSS rules (i.e. you cannot `@extend` a utility from within a standard iotaCSS breakpoint).

If, for example for consistency, you wanted to restrict the available widths to only 0%, 50%, or 100%, and had created those options in the `_width.scss` utility, then using `@extend %u-width-60pc--from-medium` would fail, whereas `@include breakpoint(from-medium) { width: 60% }` would obviously not.
