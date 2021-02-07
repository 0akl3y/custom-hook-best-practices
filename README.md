# Custom Hooks Dos & Dont's

Some tests to demonstrate the reference equality of custom hook return values:

```
yarn test
```

## TL;DR

```ts
// Returning simple data types like
// - string
// - number
// - undefined
// - null
// - boolean
// is not problematic.
// Example:
export const useString = () => "my static string";

// Retuning global object variables is not problematic.
const object = { hello: "world" };
export const useGlobalObject = () => object;

// Also nested global object references is not problematic.
const nestedObject = { nested: { hello: "world" } };
export const useNestedGlobalObject = () => nestedObject.nested;

// For non global objects in custom hooks use useRef.
// Don't return the plain object
export const useCustom = () => {
  const refObject = useRef({ hello: "world" });
  return refObject.current;
};

// If you want to define default values in redux selectors, which are not simple data types, use global variables.
// Don't define non simple data types inline as default values.
const defaultValue = { count: 0 };
export const useCustom = () => {
  return useSelector<ReturnType<typeof rootReducer>>(
    (state) => state.counter?.value1 ?? defaultValue
  );
};

// If you want to filter a list in a redux selector use useRef and dequal (npm package).
// Don't return the plain filtered value.
export const useCustom = () => {
  const filteredState = useSelector<ReturnType<typeof rootReducer>>((state) =>
    state.list.filter((_, i) => i % 2 === 0)
  );
  const filteredRef = useRef(filteredState);
  if (!dequal(filteredState, filteredRef.current)) {
    filteredRef.current = filteredState;
  }
  return filteredRef.current;
};
```
