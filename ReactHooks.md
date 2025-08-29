# React Hooks

## TypeScript

All hooks must be written in TypeScript and should include tests.

## The `any` type

The `any` type should be avoided.  Having trouble figuring out a type? Feel free to reach out; It can be difficult.

## Rules of Hooks

You must follow the [rules of hooks](https://reactjs.org/docs/hooks-rules.html).  Also, check out [building your own hooks](https://reactjs.org/docs/hooks-custom.html).

## Folder structure

Inside the hooks folder create a sub-folder for your hook. The folder should match the name of your hook. Each hook should have the following files:

- useHook.ts - the custom hook
- useHook.spec.tsx - the tests
- useHook.stories.tsx|mdx - hook stories
- index.ts - export file

Additional files can be included as needed.

## Use JSDoc comments to document hook

All parameters of the hook should be documented with JSDoc comments.
If needed a `type` object can be used for the "props" of the hook.

```ts
type useMyHookProps = {
  param1: string
};

const useMyHook = ({param1}: useMyHookProps ) => {};

```

## Add a `default export`

```ts
export type useMyHookProps = {};
function useMyHook = () => {};
export default useMyHook;
```

## Unit tests are written with Testing Library

All testing is done with Jest and [Testing Library](https://testing-library.com/) (React Hooks in this case).  
No `Enzyme`, `Sinon`, `Selenium`, etc. will be accepted.

## Unit tests should test all available props

If you have a property on your component, it needs to be tested to make sure that the code is doing what you expect.  Currently, there are no code coverage standards, however, you should cover as many use cases as possible in your tests as this might be implemented in the future.

## Scope Creep

Have an end goal for your hook. In other words, draw a line in the sand with your functionality. Make sure you have your core functionality working before adding on other features.  Remember, you can add enhancements later with another MR. Once you have that in place, submit an MR.
