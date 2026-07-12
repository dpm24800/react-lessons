# Chapter 3: Functional vs. Class Components

## 1. Concept Explanation

In React, components are the building blocks of the user interface. Over the history of React, components have been written in two primary ways: **Functional Components** and **Class Components**.

### Functional Components (The Modern Standard)

Initially, functional components were simple JavaScript functions that took in `props` (properties) as an argument and returned JSX. They were purely presentation-focused and could not manage their own internal lifecycle or state.

However, in 2019, React introduced **Hooks** (like `useState` and `useEffect`). Hooks allowed functional components to fully manage state and lifecycle events. Today, functional components are the modern standard for writing React applications.

### Class Components (The Legacy Paradigm)

Before Hooks, if a component needed to manage state or trigger logic at specific lifecycle points, it *had* to be written as a Class Component. Class components are standard JavaScript classes that extend `React.Component`. They require a mandatory `render()` method to return JSX and access data via `this.props` and `this.state`.

While modern apps are built almost exclusively with functions, thousands of production applications built before 2019 still rely on class components, making them crucial to understand for real-world development and legacy maintenance.

---

## 2. Code Blueprint

Let's compare the exact same component—a simple counter profile—written using both approaches.

### Modern Functional Component (Standard)

```javascript
import React, { useState } from 'react';

function FunctionalCounter() {
  // Declaring state using the useState hook
  const [count, setCount] = useState(0);

  return (
    <div className="component-card functional">
      <h3>Modern Functional Component</h3>
      <p>Current Count: {count}</p>
      {/* State updates use the direct setter function */}
      <button onClick={() => setCount(count + 1)}>
        Increment Count
      </button>
    </div>
  );
}

export default FunctionalCounter;

```

### Legacy Class Component (Maintenance Mode)

```javascript
import React inside 'react';

class ClassCounter extends React.Component {
  // State initialization happens within a constructor method
  constructor(props) {
    super(props); // Required to hook into React.Component capabilities
    this.state = {
      count: 0
    };
  }

  render() {
    return (
      <div className="component-card class-based">
        <h3>Legacy Class Component</h3>
        {/* Accessing state requires referencing 'this' */}
        <p>Current Count: {this.state.count}</p>
        
        {/* State updates require calling this.setState() */}
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Increment Count
        </button>
      </div>
    );
  }
}

export default ClassCounter;

```

---

## 3. Real-World Use Cases

* **New Greenfields Projects:** 100% Functional Components. Every modern repository, documentation framework, and official guide assumes you are writing functional components with hooks.
* **Enterprise Modernization Contracts:** Reading, maintaining, and gradually refactoring legacy codebases from Class Components into Functional Components without breaking the production ecosystem.

---

## 4. When to Use vs. When NOT to Use

### Functional Components:

* **When to use:** For all new components, features, and modern projects. They offer better performance, cleaner code separation, and compatibility with the entire modern React ecosystem.

### Class Components:

* **When to use:** *Only* when you are working on a legacy application that cannot be easily updated, or if you encounter an exceptionally rare edge case like an ancient Error Boundary component (though modern alternatives or libraries usually handle this now).
* **When NOT to use:** Do not write new Class Components in a modern codebase. They add unnecessary weight and syntax overhead.

---

## 5. Advantages & Disadvantages

| Component Type | Advantages | Disadvantages |
| --- | --- | --- |
| **Functional Components** | • Less boilerplate code; short and highly readable.<br>

<br>• Simple to test and isolate.<br>

<br>• Tree-shakable (better bundle optimization). | • Requires understanding the rules of Hooks (e.g., hooks cannot be used inside loops or conditionals). |
| **Class Components** | • Explicit separation of lifecycle phases.<br>

<br>• Highly structured architecture layout. | • Heavy boilerplate (`constructor`, `super`, `this` binding everywhere).<br>

<br>• Harder for build tools to minify/compress.<br>

<br>• Complex to reuse logic between classes without nesting patterns. |

---

## 6. Common Beginner Mistakes & Fixes

### Mistake 1: Forgetting `this.` in Class Components

Beginners working in class components often try to read data directly via `props.name` or `state.value`.

* **The Error Message:** `ReferenceError: props is not defined`
* **Why it's wrong:** Class components wrap their variables inside the class instance context. You must explicitly look for them on the instantiated object using `this`.
* **The Fix:** Always append the execution target prefix: `this.props.name` or `this.state.value`.

### Mistake 2: Mutating Class State Directly

Trying to modify class state directly like this: `this.state.count = 5`.

* **Why it's wrong:** React cannot track a direct assignment mutation on an object. Because React is unaware that the variable changed, it will not trigger a re-render, leaving the UI out of sync.
* **The Fix:** Always use the built-in update pipeline method: `this.setState({ count: 5 })`.