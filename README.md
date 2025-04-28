# React coding guidelines

This is a code style guide with best practices and additional notes on performance & optimizations.

Following the style guide will help maintain consistency and readability of code across a project and team, which can improve code quality and reduce errors. It can also make it easier for new developers to onboard and understand the codebase.

## Table of Contents

- [React coding guidelines](#react-coding-guidelines)
  - [Table of Contents](#table-of-contents)
  - [React](#react)
      - [Examples](#examples)
        - [Bad Code Example:](#bad-code-example)
        - [Good Code Examples:](#good-code-examples)
    - [Performance \& Optimizations](#performance--optimizations)


## React
- Use functional components instead of class components.
- Use the `useState` and useEffect hooks for managing component state and side effects.
- Use conditional rendering to control the display of components.
- Use `React.memo` to memoize functional components for performance optimization.
- Use the `useContext` hook to access global data without passing props down through multiple components.
- Use the `useCallback` hook to memoize callback functions and avoid unnecessary re-renders.
- Avoid using anonymous arrow functions inside JSX tree as this can cause unnecessary re-renders of the component, since a new function is created on each render.

#### Examples

##### Bad Code Example:

```
import React, { useState } from 'react';

function MyComponent(props) {
  const [count, setCount] = useState(0);

  return (
    <div>
      <h1>Current Count: {count}</h1>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  )
}

```

This code has the following formatting issues:

* The logic is directly placed inside the onClick event handler, making it harder to read and maintain the code.
Using an anonymous arrow function inside onClick can cause unnecessary re-renders of the component, since a new function is created on each render.


##### Good Code Examples:

```
import React, { useState } from 'react';

function MyComponent(props) {
  const [count, setCount] = useState(0);

  function handleIncrement() {
    setCount(count + 1);
  }

  return (
    <div>
      <h1>Current Count: {count}</h1>
      <button onClick={handleIncrement}>Increment</button>
    </div>
  );
}
```
This code has the following formatting improvements:

* The handleIncrement function is defined and then passed as a reference to the onClick event handler, making the code more readable and maintainable.
The useState hook is used to manage the component's state, rather than directly modifying it, which is a best practice in React development.

### Performance & Optimizations

 List of guidelines on how to write an optimized for performance React / Redux application:


* Use the latest versions of React and Redux: Always make sure you are using the late

* Use the useSelector and useDispatch hooks: Use the useSelector and useDispatch hooks provided by React-Redux to optimize your data flow. These hooks use the useSelector and useDispatch functions from Redux internally and provide a more concise syntax.

* Use memoized selectors: Use memoized selectors to avoid unnecessary computations and re-renders. Memoization is a technique of caching the results of a function so that it can be returned without re-computing if the input arguments remain the same.

* Normalize your Redux state: Normalize your Redux state to optimize data retrieval and management. Normalization is a technique of structuring your state in a way that allows you to easily query it and avoid unnecessary computations.

* Use lazy loading: Use lazy loading to improve the initial load time of your application. Lazy loading is a technique of deferring the loading of non-critical parts of your application until they are actually needed.

* Optimize images and media: Optimize your images and media by compressing them and using optimized formats such as WebP. Also, consider using lazy loading to defer the loading of images until they are needed.

* Use server-side rendering: Use server-side rendering to improve the initial load time of your application. Server-side rendering is a technique of rendering your application on the server and sending the HTML to the client, which can be displayed immediately.

* Use code splitting: Use code splitting to reduce the size of your JavaScript bundle and improve the initial load time of your application. Code splitting is a technique of splitting your JavaScript code into smaller chunks that can be loaded on demand.

* Use caching: Use caching to improve the performance of your application by avoiding unnecessary network requests. Caching is a technique of storing the results of network requests so that they can be returned immediately if the same request is made again.

* Use compression: Use compression to reduce the size of your network requests and improve the performance of your application. Compression is a technique of compressing your network requests so that they can be transferred more quickly.

* Monitor performance: Use tools such as Google Analytics and New Relic to monitor the performance of your application and identify bottlenecks. This will allow you to make targeted optimizations to improve the overall performance of your application.

* Test and debug: Use automated testing and debugging tools such as Jest, Enzyme, and React Developer Tools to ensure that your application is optimized and error-free.