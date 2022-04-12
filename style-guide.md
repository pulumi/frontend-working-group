# Pulumi Frontend Style Guide

## Intro
At Pulumi, we have a lot of frontend surfaces - our marketing website, docs, Service console, and HubSpot, among others.  This style guide is meant as a collection of recommendations for code across all the surfaces.

The goals of this guide are:
- Create consistency across the many different frontends we have
- Offer a starting point for folks who make frontend changes less frequently
- Lower the amount of discussion needed in PRs about code style decisions

This is a resource for teams - you might choose to use this style guide fully, or intentionally deviate in some way.  

This guide is currently a work in-progress.  You might notice some headers without content, or see something not covered that you would expect. Please make a PR or issue for anything you’d like to see.

### References
Portions of this style guide are pulled from or inspired by the following sources.  Sources have been linked wherever used directly:
- https://github.com/airbnb/javascript
- https://developer.mozilla.org/en-US/docs/MDN/Guidelines/Code_guidelines/JavaScript
- https://developer.mozilla.org/en-US/docs/MDN/Guidelines/Code_guidelines/CSS
- https://google.github.io/styleguide/htmlcssguide.html

## Formatting
### Rely on Prettier whenever possible
Ideally, PRs don’t have to worry about formatting issues (commas, semicolons, spaces, line length).  Prettier can be enabled on auto-save in editors to make consistent formatting effortless.  For the settings you might want to customize, you can use a .prettierrc.json file.
<!-- TODO: Link to example .prettierrc being used in some repos - call out some items like double quotes, 4 spaces -->

### Use ESLint


## File organization + naming conventions
<!-- TODO: Link to Brant’s doc + other relevant resources -->

## Developing for mobile
Consider mobile-first development - build up, don’t pare down.  We use a shared set of breakpoints across our frontend surfaces, and you should consider how your change will appear at each of those breakpoints.  If you intentionally are not considering a breakpoint for any reason, it’s best practice to note that in the code.

Write CSS styles for mobile first, then override those styles with media queries targeting successive viewport widths with `@media (min-width: )` ([Source](https://developer.mozilla.org/en-US/docs/MDN/Guidelines/Code_guidelines/CSS#use_mobile_first_media_queries)):

/* Default CSS layout for narrow screens. */
button {
    color: black;
}

/* CSS for medium width screens. */
@media (min-width: 480px) {
    button {
        color: red;
    }
}

/* CSS for wide screens. */
@media (min-width: 800px) {
    button {
        color: green;
    }
}

/* CSS for really wide screens. */
@media (min-width: 1100px) {
    button {
        color: blue;
    }
}

<!-- TODO: link to mobile breakpoint code paths -->

## CSS
Guide to terminology: https://github.com/airbnb/css#terminology

### Naming 
- Prefer dashes over camelCasing in class names (https://github.com/airbnb/css).
- Prefer dash-cased variable names (e.g. $my-variable) over camelCased or snake_cased variable names.
- Use human readable selectors that describe what element(s) they style. [Source](https://developer.wordpress.org/coding-standards/wordpress-coding-standards/css/).

```
/* Not recommended: meaningless */
.abc-1901 {}

/* Not recommended: presentational */
.button-green {}
.clear {}

/* Recommended: specific */
.gallery {}
.login {}
.video {}

/* Recommended: generic */
.aux {}
.alt {}
```

### Structure

- Refrain from using over-qualified selectors: `div.container` can simply be stated as `.container`.
- Try to avoid using IDs as selectors, as they are overly specific.

### Property ordering
<!-- TODO: link CSS Wizardry's article on dealing with specificity -->
Group properties together meaningfully.  Here's a suggested ordering from [Wordpress](https://developer.wordpress.org/coding-standards/wordpress-coding-standards/css/#property-ordering).

1. Display
2. Positioning
3. Box model
4. Colors and Typography
5. @include declarations
6. Nested selectors

### General
- Write detailed comments for code that isn't self-documenting.  Examples might include: uses of z-index, compatibility or browser-specific hacks.
- Mixins should be used to DRY up your code, add clarity, or abstract complexity--in much the same way as well-named functions.
- Use flexible/relative units.
- Favor longhand rules over terse shorthand.

Shorthand is preferable for single properties - for example, `padding: 2px 4px` instead of `padding-top` and `padding-bottom`.  But when combining multiple properties, it is often harder to understand what the shorthand is doing. It takes a while to pick apart exactly what the font syntax below is doing.  [Source](https://developer.mozilla.org/en-US/docs/MDN/Guidelines/Code_guidelines/CSS#favor_longhand_rules_over_terse_shorthand).

`font: small-caps bold 2rem/1.5 sans-serif;`

Whereas this is more immediate in terms of understanding:
```
font-variant: small-caps;
font-weight: bold;
font-size: 2rem;
line-height: 1.5;
font-family: sans-serif;
```

## JavaScript

### Use TypeScript

We generally prefer TypeScript over vanilla JavaScript. Occasionally this may not be practical, and that's okay, but as a general rule, if you _can_ use TypeScript, you should.

### Use property value shorthand.
Why? It is shorter and descriptive. [Source](https://github.com/airbnb/javascript#es6-object-concise).

```
const lukeSkywalker = 'Luke Skywalker';

// bad
const obj = {
  lukeSkywalker: lukeSkywalker,
};

// good
const obj = {
  lukeSkywalker,
};
```

### Use array spreads (`...`) to copy arrays.
[Source](https://github.com/airbnb/javascript#es6-array-spreads).

```
// bad
const len = items.length;
const itemsCopy = [];
let i;

for (i = 0; i < len; i += 1) {
  itemsCopy[i] = items[i];
}

// good
const itemsCopy = [...items];
```

### Use object destructuring when accessing and using multiple properties of an object.
Why? Destructuring saves you from creating temporary references for those properties, and from repetitive access of the object. Repeating object access creates more repetitive code, requires more reading, and creates more opportunities for mistakes. Destructuring objects also provides a single site of definition of the object structure that is used in the block, rather than requiring reading the entire block to determine what is used. [Source](https://github.com/airbnb/javascript#destructuring--object).


```
// bad
function getFullName(user) {
  const firstName = user.firstName;
  const lastName = user.lastName;

  return `${firstName} ${lastName}`;
}

// good
function getFullName(user) {
  const { firstName, lastName } = user;
  return `${firstName} ${lastName}`;
}

// best
function getFullName({ firstName, lastName }) {
  return `${firstName} ${lastName}`;
}
```

### Use array destructuring. eslint: prefer-destructuring
[Source](https://github.com/airbnb/javascript#destructuring--array).

```
const arr = [1, 2, 3, 4];

// bad
const first = arr[0];
const second = arr[1];

// good
const [first, second] = arr;
```

### Use named function expressions instead of function declarations.
Why? Function declarations are hoisted, which means that it’s easy - too easy - to reference the function before it is defined in the file. This harms readability and maintainability. If you find that a function’s definition is large or complex enough that it is interfering with understanding the rest of the file, then perhaps it’s time to extract it to its own module! Don’t forget to explicitly name the expression, regardless of whether or not the name is inferred from the containing variable (which is often the case in modern browsers or when using compilers such as Babel). This eliminates any assumptions made about the Error’s call stack. [Source](https://github.com/airbnb/javascript#functions--declarations).


```
// bad
function foo() {
  // ...
}

// bad
const foo = function () {
  // ...
};

// good
// lexical name distinguished from the variable-referenced invocation(s)
const short = function longUniqueMoreDescriptiveLexicalFoo() {
  // ...
};
```
 
### When you must use an anonymous function (as when passing an inline callback), use arrow function notation.
Why? It creates a version of the function that executes in the context of this, which is usually what you want, and is a more concise syntax.
Why not? If you have a fairly complicated function, you might move that logic out into its own named function expression.
[Source](https://github.com/airbnb/javascript#arrows--use-them).


```
// bad
[1, 2, 3].map(function (x) {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
```

### Use === and !== over == and !=
[Source](https://github.com/airbnb/javascript#comparison--eqeqeq).


### Use shortcuts for booleans, but explicit comparisons for strings and numbers.
[Source](https://github.com/airbnb/javascript#comparison--shortcuts).

```
// bad
if (isValid === true) {
  // ...
}

// good
if (isValid) {
  // ...
}

// bad
if (name) {
  // ...
}

// good
if (name !== '') {
  // ...
}

// bad
if (collection.length) {
  // ...
}

// good
if (collection.length > 0) {
  // ...
}
```

### Ternaries should not be nested and generally be single line expressions.
[Source](https://github.com/airbnb/javascript#comparison--nested-ternaries).

```
// bad
const foo = maybe1 > maybe2
  ? "bar"
  : value1 > value2 ? "baz" : null;

// split into 2 separated ternary expressions
const maybeNull = value1 > value2 ? 'baz' : null;

// better
const foo = maybe1 > maybe2
  ? 'bar'
  : maybeNull;

// best
const foo = maybe1 > maybe2 ? 'bar' : maybeNull;

#### Avoid unneeded ternary statements. eslint: no-unneeded-ternary
// bad
const foo = a ? a : b;
const bar = c ? true : false;
const baz = c ? false : true;

// good
const foo = a || b;
const bar = !!c;
const baz = !c;
```

### TODOs in code comments should always be accompanied by a ticket link
That ticket should ideally include an item to remove the TODO line in the code.

### Use camelCase when naming objects, functions, and instances.
[Source](https://github.com/airbnb/javascript#naming--camelCase).

```
// bad
const OBJEcttsssss = {};
const this_is_my_object = {};
function c() {}

// good
const thisIsMyObject = {};
function thisIsMyFunction() {}
```

### Acronyms and initialisms should always be all uppercased, or all lowercased.
Why? Names are for readability, not to appease a computer algorithm. [Source](https://github.com/airbnb/javascript#naming--Acronyms-and-Initialisms).


```
// bad
import SmsContainer from './containers/SmsContainer';

// bad
const HttpRequests = [
  // ...
];

// good
import SMSContainer from './containers/SMSContainer';

// good 
const HTTPRequests = [ 
  // ... 
] ;

// also good
const httpRequests = [
  // ...
];

// best 
import TextMessageContainer from './containers/TextMessageContainer' ;

// best
const requests = [
  // ...
];
```

### You may optionally uppercase a constant only if it (1) is exported, (2) is a const (it can not be reassigned), and (3) the programmer can trust it (and its nested properties) to never change.
Why? This is an additional tool to assist in situations where the programmer would be unsure if a variable might ever change. UPPERCASE_VARIABLES are letting the programmer know that they can trust the variable (and its properties) not to change.
What about all const variables? - This is unnecessary, so uppercasing should not be used for constants within a file. It should be used for exported constants however.
What about exported objects? - Uppercase at the top level of export (e.g. EXPORTED_OBJECT.key) and maintain that all nested properties do not change. [Source](https://github.com/airbnb/javascript#naming--uppercase).

```
// bad
const PRIVATE_VARIABLE = 'should not be unnecessarily uppercased within a file';

// bad
export const THING_TO_BE_CHANGED = 'should obviously not be uppercased';

// bad
export let REASSIGNABLE_VARIABLE = 'do not use let with uppercase variables';

// ---

// allowed but does not supply semantic value
export const apiKey = 'SOMEKEY';

// better in most cases
export const API_KEY = 'SOMEKEY';

// ---

// bad - unnecessarily uppercases key while adding no semantic value
export const MAPPING = {
  KEY: 'value'
};

// good
export const MAPPING = {
  key: 'value'
};
```

### SEO
[See this document for a more in-depth set of SEO recommendations](https://docs.google.com/document/d/1nYjdzpjSSCGXJDtUcKMaRl2OmDBkS4sb/edit).

- Only a single H1 should be used on any page.
- The H1 should contain a direct or very similar version of the target keyword for the entry.


### Frameworks and tools 
<!-- TODO: Link to/mention where each is used, and maybe link to docs?  Other best practice specifics for each? -->
Angular
Material
Tailwind
React
Storybook


