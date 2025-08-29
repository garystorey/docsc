# React Components

## TypeScript

All components must be written in TypeScript. This includes tests, and stories.
The Levvel CLI can convert these TypeScript components into JavaScript as needed. 🤯

## The `any` type

The `any` type should be avoided.  Having trouble figuring out a type? Feel free to reach out.

## Naming

Inside the components folder create a sub-folder for your component. The folder should match the name of your component.  Both the folder and the component files should start with an uppercase character. Each component should have the following files:

- `Component.tsx` - the component
- `Component.spec.tsx` - the tests
- `Component.stories.tsx|mdx` - the stories
- `Component.module.(s)css` - the (s)css module

Additional files can be included as needed. Breaking larger components into smaller sub-components is encouraged.  For example, an `Accordion` could have an `AccordionHeader`, `AccordionContent` and `AccordionButton` sub-components.  Depending on how many you have, these might need to be added to their own sub folder.  

The subcomponents functionality should be tested in the regular component tests. An accordion cant work without the content, so your `Accordion` test should fail even though technically the problem is `AccordionContent`. Make sense?

## Import in order

**Import packages in reverse order**. In other words, third party dependencies come first, followed by project level dependencies, followed by local dependencies, then non-code imports.  For example, `React` and `axios` are both third party components and should be first.  Project levels imports are ordered by how far they are from the current folder.  So those that are three folders away would be first, followed by those that are two folders away, etc.  Local components, or files in the current folder, come next. Lastly, import any other related files such as images or SCSS files.

The below is a completely contrived example:

```tsx
// third party
import {useState, useEffect, Fragment} from 'react';
import axios from 'axios';

// two folders away
import {get} from '../../utils';

// one folder away
import Label from '../Label';
import VisuallyHidden from '../VisuallyHidden';

// current folder
import AccordionHeader from './AccordionHeader';
import AccordionContent from './AccordionContent';
import AccordionButton from './AccordionButton';

// other 
import './Accordion.scss';
```

## Use function declaration for your Component

Prefer a `function declaration` over a `function expression` when creating your component.  Although you can use arrow functions for your component, using the `function` keyword allows the name to be read by React Developer Tools automatically.  Otherwise, you will need to add a `displayName` attribute to your component.  This *only applies to the main Component* function.  Feel free to use arrow constants with event handlers, etc.

```tsx
// Good - function declaration
function Accordion(props) {}

// Bad - function expression
const Accordion = () => {}
 // now we have to add this 😦 
Acccordion.displayName = 'Accordion';
```

## Use `PropsWithChildren` for components with `children`

If your component wraps content, you should use the `PropsWithChildren` type for your `Props` object.

```tsx
type ModalProps = PropsWithChildren<{
  // custom properties here
}>;
const Modal = ({children}: ModalProps) => {};
```

## Extend from a base Element

If you are basing your component on an existing HTML Element (*input, button, etc.*), then your `Props` should extend from that base object.  For example, with an `Input` component your `Props` object would extend the base `InputHTMLElement`.

```tsx
type InputProps = InputHTMLElement<HTMLInputElement> & PropsWithChildren<{
  // custom properties here. all standard properties are available
}>;
```

## Name your Props object after your Component

When creating new `Props`, name the object after your component.  If you are working on an `Input` component, then you would use `InputProps` as the name of your Props object.  **DO NOT** name your object `Props`.😤

> **Do not name your object `Props`** - Us

## Prefer functional components

Functional components are easier to read and understand.  They also allow for functionality to be added using `hooks`. Oh, look over there! 👀 We already have some hooks for you to use!

## Use the functional form of `useState` when appropriate

When updating state based on the previous value, you should **always use the functional form of** `useState`.
Read more about [function updates and lazy initialization](https://kentcdodds.com/blog/use-state-lazy-initialization-and-function-updates) in this article.

```tsx
const [count,setCount] = useState<boolean>(0);

// Good
setCount(prevCount => prevCount+1) 

// Bad
setCount(count+1)
```

## Use `default export`

The primary reason being that named exports can not be lazy loaded.  
If your component has custom types, those should be exported as well.

```tsx
// Component.tsx
export type ComponentProps = {};
function Component() {}
export default Component;

// Page.tsx
import Component, {ComponentProps} from '../components'
```

## Levvel CLI Integration

Because of our integration with the Levvel CLI, the project does require a specific structure. So although, you personally might not like how things are setup, some things are required to be the way they are.  For example, yes, we could change the `baseUrl` in our `tsconfig` to point to the `src` folder for easier importing... but that doesn't work with the CLI.  So, keep that in mind before "offering" to update our config. 😉

## Accessibility

Accessibility 👨‍🦽🧏‍♀️🧜‍♀️(*a11y*) should be considered first and foremost in all components and **is required for inclusion in the library**. The [WCAG website](https://www.w3.org/WAI/standards-guidelines/wcag/) has documentation on how to meet accessibility standards for different types of controls. A minimum of AA compliance is acceptable (*but AAA is preferred*).  

FYI: Once you have a story created, you can verify a lot of accessibility requirements on your component story's `Accessibility` tab.😎

A11y Resources:

- [WCAG website](https://www.w3.org/WAI/standards-guidelines/wcag/)
- [Components- Accessibility First](https://components.guide/accessibility-first)
- [WAI-ARIA Authoring Practices](https://www.w3.org/TR/wai-aria-practices/)
- [W3C A11y Standards](https://www.w3.org/standards/webdesign/accessibility/)

A11y Tools

- [Color Contrast Tools](https://www.webaxe.org/color-contrast-tools/)
- [axe DevTools Chrome Extention](https://chrome.google.com/webstore/detail/axe-devtools-web-accessib/lhdoppojpmngadmnindnejefpokejbdd)
- Storybook accessibility tab!

## SCSS/CSS Modules

All components should be written using SCSS Modules (*Component.modules.scss*).  Please be aware that style linting rules will still apply.
Some basic rules to follow:

### Keep the specificity low

Because SCSS can encourage high specificity, please make an effort to keep the overall specificity as low as possible. 👀 Check out [MDN's CSS Specificity](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity) for more details.

### Dont use `&` for class name concatenation

Only use the `&` for nesting and pseudo-classes. Do NOT use it for class name concatenation.

```scss
// BAD CODE DONT DO THIS
// base class name
.mComponent {
  font-size: 1rem;
  
  // class name concatenation
  &Title {
    font-size: 1rem;
  }
```

Use the following syntax:

```scss

.component {
  font-size: 1rem;
  border: 1px solid gray;

  .componentTitle {
    font-size: 2rem;
  }

// pseudo-classes
  &:hover {
    border-color: red;
  }

// both classes on same element
  &.isPrimary {
    background-color: purple;
  } 

  // .component + .component
  & & {
    margin-bottom: 1rem;
  }
}
```

## Use Relative Units

Unless you are writing a value of `5px` or less, you **should be not be using `pixels (px)` units** but a more "relative" CSS unit.  In the majority of cases, `rem` is the best choice. By default, `1rem` == `16px`.  However, in practice, 1rem === 1 unit of the `font-size` of the `body` element.

Other more appropriate units: `vw`, `vh`, `em`,`ch`.  

Although not required for creating a component, a good standard of practice is to set the base `font-size` as `1rem` and all internal `font-size`s to use `em`s.  This allows the most flexibility for the end user of the control. They can set the base `font-size` to any value and your component will adjust to match.

**NOTE**: Be careful of nested `em`s when using this approach. You can end up with much larger or smaller sizes then you intended!

## Unit tests are written with Testing Library

All testing is done with Jest and [Testing Library](https://testing-library.com/) (React in this case).  No `Enzyme`, `Sinon`, `Selenium`, etc. will be accepted.

## Unit tests should test all available props

If you have a property on your component, it needs to be tested to make sure that the code is doing what you expect.  Currently, there are no code coverage standards, however, you should cover as many use cases as possible in your tests as this might be implemented in the future.

## Stories are written in Storybook 6 CSF/MDX format

This is the current standard for writing stories.  [Check out the Storybook site](https://storybook.js.org/docs/react/writing-stories/args) for more information. **NOTE:** If your stories require additional styles, add them to `preview.scss` in the `storybook` folder. This keeps these styles separated from the main stylesheets.  Also, remember styles are now scoped, so be careful adding styles to the global `preview.scss`.

## Committing your Code

This repo uses `commitlint` to implement standardized commit messages (no "updates" as commit messages).  Not sure what format to follow?
Just run `npm commit` and it will walk you through creating the message.

## Scope Creep

**Have an end goal for your component**. Make sure you have your core functionality working before adding on other features.  Remember, you can add enhancements later. Once you have that in place, add your stories (*you already wrote your tests right?* 😉) and submit an MR.
