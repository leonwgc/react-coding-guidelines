# React coding guidelines

This is a code style guide with best practices and additional notes on performance & optimizations. Following the style guide will help maintain consistency and readability of code across a project and team, which can improve code quality and reduce errors. It can also make it easier for new developers to onboard and understand the codebase.

- [React coding guidelines](#react-coding-guidelines)
  - [Basics](#basics)
    - [Use functional components instead of class components.](#use-functional-components-instead-of-class-components)
    - [Avoid using anonymous arrow functions inside JSX tree](#avoid-using-anonymous-arrow-functions-inside-jsx-tree)
  - [React / Redux Performance \& Optimizations](#react--redux-performance--optimizations)
    - [Use the useSelector and useDispatch hooks](#use-the-useselector-and-usedispatch-hooks)
    - [Use lazy loading](#use-lazy-loading)
    - [Use `React.memo` to memoize functional components for performance optimization.](#use-reactmemo-to-memoize-functional-components-for-performance-optimization)
  - [Hooks](#hooks)
    - [Only call Hooks at the top level, don't call them inside loops, conditions, or nested functions.](#only-call-hooks-at-the-top-level-dont-call-them-inside-loops-conditions-or-nested-functions)
    - [Only call Hooks in React function components and custom Hooks, not in regular JavaScript functions.](#only-call-hooks-in-react-function-components-and-custom-hooks-not-in-regular-javascript-functions)
    - [useEffect and similar Hooks should include all dependencies in the dependency array](#useeffect-and-similar-hooks-should-include-all-dependencies-in-the-dependency-array)
    - [Use the `useState` and `useEffect` hooks for managing component state and side effects.](#use-the-usestate-and-useeffect-hooks-for-managing-component-state-and-side-effects)
    - [Use the `useContext` hook to access global data without passing props down through multiple components.](#use-the-usecontext-hook-to-access-global-data-without-passing-props-down-through-multiple-components)
    - [Use the `useCallback` hook to memoize callback functions and avoid unnecessary re-renders.](#use-the-usecallback-hook-to-memoize-callback-functions-and-avoid-unnecessary-re-renders)
    - [Use the `useMemo` hook to cache the result of a calculation between re-renders.](#use-the-usememo-hook-to-cache-the-result-of-a-calculation-between-re-renders)
  - [Accessibility](#accessibility)
    - [The img tag should include an alt attribute](#the-img-tag-should-include-an-alt-attribute)
    - [Do not use keywords like "image," "photo," or "picture" in the alt attribute of the img tag. Screen readers already identify img elements as images, so including such keywords in the alt attribute is unnecessary.](#do-not-use-keywords-like-image-photo-or-picture-in-the-alt-attribute-of-the-img-tag-screen-readers-already-identify-img-elements-as-images-so-including-such-keywords-in-the-alt-attribute-is-unnecessary)
    - [Anchor elements (i.e. a elements) must contain content and the content must be visible to screen readers](#anchor-elements-ie-a-elements-must-contain-content-and-the-content-must-be-visible-to-screen-readers)
    - [Do not use invalid ARIA attributes, only use the aria-\* attributes listed in the WAI-ARIA States and Properties spec.](#do-not-use-invalid-aria-attributes-only-use-the-aria--attributes-listed-in-the-wai-aria-states-and-properties-spec)
    - [ARIA attributes and states must have valid values.](#aria-attributes-and-states-must-have-valid-values)
    - [Do not add role or aria-\* attributes to specific elements. Some reserved DOM elements do not support setting ARIA roles or attributes, usually because they are not visible, such as meta, html, script, style](#do-not-add-role-or-aria--attributes-to-specific-elements-some-reserved-dom-elements-do-not-support-setting-aria-roles-or-attributes-usually-because-they-are-not-visible-such-as-meta-html-script-style)
    - [Only use valid, non-abstract ARIA roles](#only-use-valid-non-abstract-aria-roles)
    - [Elements with an ARIA role must also declare the properties required by that role](#elements-with-an-aria-role-must-also-declare-the-properties-required-by-that-role)

## Basics

### Use functional components instead of class components.
优先使用函数组件，避免使用类组件

  ```js
  // bad
  class MyComponent extends React.Component {
    render() {
      return <div>Hello</div>
    }
  }

  // good
  function MyComponent() {
    return <div>Hello</div>
  }
  ```

 ### Avoid using anonymous arrow functions inside JSX tree
  as this can cause unnecessary re-renders of the component, since a new function is created on each render.
  在JSX 中避免使用匿名箭头函数防止不必要的重新渲染

  ```js
  // bad
  import React, { useState } from 'react';

  function Counter() {
    const [count, setCount] = useState(0);

    return (
      <div>
        <h1>Count: {count}</h1>
        {/* Anonymous arrow function inside JSX */}
        <button onClick={() => setCount(count + 1)}>Increment</button>
      </div>
    );
  }

  export default Counter;

  // good
  import React, { useState, useCallback } from 'react';

  function Counter() {
    const [count, setCount] = useState(0);

    // Memoize the handler to ensure it doesn't change between renders
    const increment = useCallback(() => {
      setCount((prevCount) => prevCount + 1);
    }, []);

    return (
      <div>
        <h1>Count: {count}</h1>
        {/* Use the memoized function */}
        <button onClick={increment}>Increment</button>
      </div>
    );
  }

  export default Counter;
  ```

## React / Redux Performance & Optimizations

### Use the useSelector and useDispatch hooks
1. useSelector and useDispatch provide a concise and modern way to interact with the Redux store, replacing the older connect HOC.
2. They improve readability and reduce boilerplate code.
```js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';

// Redux slice (for demonstration purposes)
const initialState = { count: 0 };

function counterReducer(state = initialState, action) {
  switch (action.type) {
    case 'increment':
      return { ...state, count: state.count + 1 };
    case 'decrement':
      return { ...state, count: state.count - 1 };
    default:
      return state;
  }
}

// Action creators
const increment = () => ({ type: 'increment' });
const decrement = () => ({ type: 'decrement' });

function Counter() {
  // Access state using useSelector
  const count = useSelector((state) => state.count);

  // Dispatch actions using useDispatch
  const dispatch = useDispatch();

  return (
    <div>
      <h1>Count: {count}</h1>
      <button onClick={() => dispatch(increment())}>Increment</button>
      <button onClick={() => dispatch(decrement())}>Decrement</button>
    </div>
  );
}

export default Counter;
```

### Use lazy loading
1. Use lazy loading to improve the initial load time of your application.
2. Lazy loading is a technique of deferring the loading of non-critical parts of your application until they are actually needed.

  ```js
    import React, { Suspense } from 'react';

  // Lazy load the component
  const LazyLoadedComponent = React.lazy(() => import('./LazyLoadedComponent'));

  function App() {
    return (
      <div>
        <h1>Welcome to the App</h1>
        {/* Use Suspense to show a fallback while the component is being loaded */}
        <Suspense fallback={<div>Loading...</div>}>
          <LazyLoadedComponent />
        </Suspense>
      </div>
    );
  }

  export default App;
  ```
1. React.lazy: Dynamically imports the LazyLoadedComponent only when it is needed.This reduces the initial bundle size, improving the application's load time.
2. Suspense: Wraps the lazy-loaded component and provides a fallback UI (e.g., a loading spinner or message) while the component is being loaded.

### Use `React.memo` to memoize functional components for performance optimization.
 使用 `React.memo` 来优化性能，避免不必要的重新渲染
  ```js
  // Example of using React.memo to optimize performance
  import React from 'react';

  // A functional component that renders a simple message
  function Message({ text }) {
    console.log('Message component rendered');
    return <p>{text}</p>;
  }

  // Wrapping the component with React.memo to prevent unnecessary re-renders
  const MemoizedMessage = React.memo(Message);

  function App() {
    const [count, setCount] = React.useState(0);

    return (
      <div>
        <h1>Count: {count}</h1>
        <button onClick={() => setCount(count + 1)}>Increment</button>
        {/* MemoizedMessage will only re-render if the 'text' prop changes */}
        <MemoizedMessage text="This is a memoized message" />
      </div>
    );
  }

  export default App;
  ```

   React.memo is a higher-order component used to wrap functional components.
  It performs a shallow comparison of the component's props and only re-renders the component if the props have changed.

  The MemoizedMessage component is wrapped with React.memo, so even if the parent component App re-renders, MemoizedMessage will not re-render as long as the text prop remains unchanged.


## Hooks
### Only call Hooks at the top level, don't call them inside loops, conditions, or nested functions.
 只在最顶层调用 Hooks，不要在循环、条件和嵌套函数中调用 Hooks
```js
// bad - call Hooks inside conditions
function ComponentWithConditionalHook() {
  if (cond) {
    useConditionalHook();
  }
}

// bad - call Hooks inside loops
function ComponentWithHookInsideLoop() {
  while (cond) {
    useHookInsideLoop();
  }
}

// bad - call Hooks inside callback
function ComponentWithHookInsideCallback() {
  useEffect(() => {
    useHookInsideCallback();
  });
}

// good
function ComponentWithHook() {
  useHook();
}```

* Hook names must start with use and be camelCase (Hooks 命名必须以 use 开头，小驼峰形式)

```js
// bad
const customHook = () => {}

// good
const useCustomHook = () => {}
```

### Only call Hooks in React function components and custom Hooks, not in regular JavaScript functions.
 只在 React 函数组件和自定义 Hooks 中调用 Hooks，不能在普通的 JavaScript 函数中调用 Hooks。

```js
// bad - call Hooks inside class componennt
class ClassComponentWithHook extends React.Component {
  render() {
    React.useState();
  }
}

// bad - call Hooks inside normal function
function normalFunctionWithHook() {
  useHookInsideNormalFunction();
}

// good - call Hooks inside function component
function ComponentWithHook() {
  useHook();
}

// good - call Hooks inside custom Hooks
function useHookWithHook() {
  useHook();
}
```

### useEffect and similar Hooks should include all dependencies in the dependency array
 useEffect 及类似 Hooks 需要声明所有依赖项在依赖项数组中

```js
// bad
function MyComponent() {
  const local = {};
  useEffect(() => {
    console.log(local);
  }, []);
}

// good
function MyComponent() {
  const local = {};
  useEffect(() => {
    console.log(local);
  }, [local]);
}
```
### Use the `useState` and `useEffect` hooks for managing component state and side effects.
 使用 `useState` 和 `useEffect` 钩子来管理组件状态和副作用

  ```js
  // Bad Code Example:
  import React, { Component } from 'react';

  class MyComponent extends Component {
    constructor(props) {
      super(props);
      this.state = {
        count: 0
      };
    }

    componentDidMount() {
      document.title = `Current Count: ${this.state.count}`;
    }

    componentDidUpdate() {
      document.title = `Current Count: ${this.state.count}`;
    }

    render() {
      return (
        <div>
          <h1>Current Count: {this.state.count}</h1>
          <button onClick={() => this.setState({ count: this.state.count + 1 })}>
            Increment
          </button>
        </div>
      );
    }
  }

  // Good Code Example:
  import React, { useState, useEffect } from 'react';

  function MyComponent() {
    const [count, setCount] = useState(0);

    useEffect(() => {
      document.title = `Current Count: ${count}`;
    }, [count]);

    return (
      <div>
        <h1>Current Count: {count}</h1>
        <button onClick={() => setCount(count + 1)}>Increment</button>
      </div>
    );
  }
  ```

  ### Use the `useContext` hook to access global data without passing props down through multiple components.
 使用useContext hook 存取全局数据， 防止组件层层传递props
  ```js
  // Example of using useContext to access global data
  import React, { createContext, useContext, useState } from 'react';

  // Create a Context
  const UserContext = createContext();

  // A provider component to wrap the app and provide the context value
  function UserProvider({ children }) {
    const [user, setUser] = useState({ name: 'John Doe', age: 30 });

    return (
      <UserContext.Provider value={{ user, setUser }}>
        {children}
      </UserContext.Provider>
    );
  }

  // A component that consumes the context value
  function UserProfile() {
    const { user } = useContext(UserContext);

    return (
      <div>
        <h1>User Profile</h1>
        <p>Name: {user.name}</p>
        <p>Age: {user.age}</p>
      </div>
    );
  }

  // A component that updates the context value
  function UpdateUser() {
    const { setUser } = useContext(UserContext);

    return (
      <button onClick={() => setUser({ name: 'Jane Doe', age: 25 })}>
        Update User
      </button>
    );
  }

  // Main App component
  function App() {
    return (
      <UserProvider>
        <UserProfile />
        <UpdateUser />
      </UserProvider>
    );
  }

  export default App;
  ```
  `Explanation`:

  createContext: Creates a context object (UserContext) to hold global data.

  UserProvider: A provider component that wraps the app and provides the context value (user and setUser) to all child components.

  useContext: Used in UserProfile and UpdateUser components to access the user data and setUser function from the context.

  `Benefits`: Avoids prop drilling by allowing components to directly access global data without passing props through intermediate components.

### Use the `useCallback` hook to memoize callback functions and avoid unnecessary re-renders.
 使用`useCallback` 缓存函数，避免不必要的重新渲染.
  ```js
    // Example of using useCallback to memoize functions
  import React, { useState, useCallback } from 'react';

  // A child component that receives a callback as a prop
  const ChildComponent = React.memo(({ onClick }) => {
    console.log('ChildComponent rendered');
    return <button onClick={onClick}>Click Me</button>;
  });

  function App() {
    const [count, setCount] = useState(0);
    const [text, setText] = useState('');

    // Memoize the callback to prevent unnecessary re-renders of ChildComponent
    const handleClick = useCallback(() => {
      console.log('Button clicked');
    }, []);

    return (
      <div>
        <h1>Count: {count}</h1>
        <button onClick={() => setCount(count + 1)}>Increment Count</button>
        <input
          type="text"
          value={text}
          onChange={(e) => setText(e.target.value)}
          placeholder="Type something"
        />
        {/* Pass the memoized callback to the child component */}
        <ChildComponent onClick={handleClick} />
      </div>
    );
  }

  export default App;
  ```
  `Explanation`:
  useCallback is used to memoize the handleClick function so that it is not recreated on every render unless its dependencies change.
  In this example, the dependency array is empty ([]), so the function remains the same across renders.

  Optimization: The ChildComponent is wrapped with React.memo, which prevents it from re-rendering unless its props change.
  Since the handleClick function is memoized, ChildComponent will not re-render unnecessarily when the parent component updates.

  `Benefits`:
  Avoids creating a new function on every render, which can prevent unnecessary re-renders of child components.
  Improves performance in components with expensive re-renders or deep component trees.

### Use the `useMemo` hook to cache the result of a calculation between re-renders.
 使用`useMemo` 缓存计算结果，避免重新计算.
  ```js
  import React, { useState, useMemo } from 'react';

  function ExpensiveCalculationComponent() {
    const [count, setCount] = useState(0);
    const [text, setText] = useState('');

    // Use useMemo to cache the result of an expensive calculation
    const expensiveCalculation = useMemo(() => {
      console.log('Performing expensive calculation...');
      let result = 0;
      for (let i = 0; i < 1000000000; i++) {
        result += i;
      }
      return result;
    }, [count]); // Recalculate only when `count` changes

    return (
      <div>
        <h1>Expensive Calculation Result: {expensiveCalculation}</h1>
        <button onClick={() => setCount(count + 1)}>Increment Count</button>
        <input
          type="text"
          value={text}
          onChange={(e) => setText(e.target.value)}
          placeholder="Type something"
        />
      </div>
    );
  }

  export default ExpensiveCalculationComponent;
 ```

1. The useMemo hook is used to memoize the result of the expensive calculation.The calculation is only re-executed when the count dependency changes.

2. Without useMemo, the expensive calculation would run on every render, even when unrelated state (like text) changes.

3. With useMemo, the calculation is skipped unless count changes, improving performance.



## Accessibility

### The img tag should include an alt attribute
 img 标签应包含 alt 属性

```js
  // bad
<img src="hello.jpg" />

// good
<img src="hello.jpg" alt="Me waving hello" />

// good - 图片无需被无障碍阅读器识别时
<button>
  <img src="icon.png" alt="" />
  Save
</button>
  ```

### Do not use keywords like "image," "photo," or "picture" in the alt attribute of the img tag. Screen readers already identify img elements as images, so including such keywords in the alt attribute is unnecessary.
 img 标签的 alt 属性不要使用 "image"，"photo"，"picture" 之类的关键词. 屏幕阅读器已会将 img 元素识别成图片，再在 alt 中包含这类关键词没有意义。

```js
// bad
<img src="hello.jpg" alt="Picture of me waving hello" />

// good
<img src="hello.jpg" alt="Me waving hello" />
```

### Anchor elements (i.e. a elements) must contain content and the content must be visible to screen readers
 锚元素(即 a 元素)必须含有内容，且内容必须对屏幕阅读器可见

```js
// bad - empty content
<a />

// bad - content not accessible to screen readers
<a><TextWrapper aria-hidden /></a>

// good
<a>Anchor Content!</a>
<a><TextWrapper /><a>
```

### Do not use invalid ARIA attributes, only use the aria-* attributes listed in the WAI-ARIA States and Properties spec.
 禁止使用无效的 ARIA 属性，只能使用列在 WAI-ARIA States and Properties spec 中的 aria-* 属性

```js
// bad - Labeled using incorrectly spelled aria-labeledby
<div id="address_label">Enter your address</div>
<input aria-labeledby="address_label">

// good - Labeled using correctly spelled aria-labelledby
<div id="address_label">Enter your address</div>
<input aria-labelledby="address_label">

```

### ARIA attributes and states must have valid values.
 ARIA 属性、状态的值必须为有效值。

```js
// bad - the aria-hidden state is of type true/false
<span aria-hidden="yes">foo</span>

// good
<span aria-hidden="true">foo</span>
```
### Do not add role or aria-* attributes to specific elements. Some reserved DOM elements do not support setting ARIA roles or attributes, usually because they are not visible, such as meta, html, script, style
 禁止特定元素包含 role 和 aria-* 属性。一些保留的 DOM 元素不支持设置 ARIA 角色或者属性，通常是因为这些元素是不可见的，例如 meta, html, script, style

```js
// bad - the meta element should not be given any ARIA attributes
<meta charset="UTF-8" aria-hidden="false" />

// good
<meta charset="UTF-8" />
```

### Only use valid, non-abstract ARIA roles
 仅使用有效的、非抽象的 ARIA roles

```js
// bad - not an ARIA role
<div role="datepicker" />

// bad - abstract ARIA role
<div role="range" />

// good
<div role="button" />
```

### Elements with an ARIA role must also declare the properties required by that role
 有 ARIA role 的元素必须也声明该 role 需要的属性

```js
// bad - the checkbox role requires the aria-checked state
<span role="checkbox" aria-labelledby="foo" tabindex="0"></span>

// good - the checkbox role requires the aria-checked state
<span role="checkbox" aria-checked="false" aria-labelledby="foo" tabindex="0"></span>
```


