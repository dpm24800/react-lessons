# Chapter 11: Class Component Lifecycle Methods (Legacy)

## 1. Concept Explanation

Before React introduced Hooks in version 16.8, **Class Components** were the only way to manage state and execute code at specific moments in a component's life. Instead of using a unified tool like `useEffect`, class components rely on individual built-in methods called **Lifecycle Methods**.

A component's life is broken down into three major phases:

1. **Mounting (Birth):** When the component is being created and inserted into the browser DOM for the very first time.
2. **Updating (Growth):** When the component's state or props change, causing it to re-render to update the user interface.
3. **Unmounting (Death):** When the component is being removed from the DOM and destroyed.

### The Core Lifecycle Methods

* **`componentDidMount()`**: Runs immediately *after* the component is rendered on the screen. This is the legacy equivalent of `useEffect(fn, [])`. This is where you make API calls, set up timers, or interact with the DOM.
* **`componentDidUpdate(prevProps, prevState)`**: Runs immediately *after* an update happens (due to state or prop modifications). This is the legacy equivalent of `useEffect(fn, [dependencies])`. It allows you to look at the previous data and compare it with the new data.
* **`componentWillUnmount()`**: Runs immediately *before* a component is destroyed and removed from the screen. This is the legacy equivalent of the `useEffect` cleanup function. Use this to clear timers or tear down event listeners.

---

## 2. Code Blueprint

Here is a complete look at a legacy class component that monitors window resizing using traditional lifecycle methods.

```javascript
import React, { Component } from 'react';

class LegacyWindowTracker extends Component {
  constructor(props) {
    super(props);
    this.state = {
      windowWidth: window.innerWidth,
      toggleFlag: false
    };

    // Binding custom methods to the class instance (required in older class setups)
    this.handleResize = this.handleResize.bind(this);
  }

  // 1. MOUNTING PHASE: Component is fully drawn on screen
  componentDidMount() {
    console.log("🟢 1. Birth: Component mounted to DOM.");
    // Attaching a global event listener
    window.addEventListener('resize', this.handleResize);
  }

  // 2. UPDATING PHASE: State or Prop changes trigger a re-render
  componentDidUpdate(prevProps, prevState) {
    // Crucial: You must check if the value actually changed to prevent infinite updates
    if (prevState.toggleFlag !== this.state.toggleFlag) {
      console.log("🟡 2. Growth: Update detected. Toggle state is now:", this.state.toggleFlag);
    }
  }

  // 3. UNMOUNTING PHASE: Component is about to be destroyed
  componentWillUnmount() {
    console.log("🔴 3. Death: Component is unmounting. Running cleanup...");
    // Cleaning up the global event listener to prevent memory leaks
    window.removeEventListener('resize', this.handleResize);
  }

  handleResize() {
    this.setState({ windowWidth: window.innerWidth });
  }

  render() {
    return (
      <div style={{ padding: '20px', border: '1px solid #777' }}>
        <h3>Legacy Lifecycle Monitor</h3>
        <p>Current Browser Width: <strong>{this.state.windowWidth}px</strong></p>
        
        <button onClick={() => this.setState({ toggleFlag: !this.state.toggleFlag })}>
          Trigger Update Event
        </button>
      </div>
    );
  }
}

export default LegacyWindowTracker;

```

---

## 3. Real-World Use Cases

* **Maintaining Legacy Codebases:** Working with enterprise financial, health, or tech applications built before 2019 that still leverage class components.
* **Writing Custom Error Boundaries:** As of mid-2026, React still requires class components to implement custom Error Boundaries using specific methods like `componentDidCatch` or `getDerivedStateFromError`.

---

## 4. When to Use vs. When NOT to Use

### When to Use:

* Only use lifecycle methods when maintaining pre-existing class components or when building an explicit `ErrorBoundary` component to catch unexpected layout crashes.

### When NOT to Use:

* Do not use lifecycle methods for any new features, layout architectures, or components. Use functional components combined with the `useEffect` hook instead.

---

## 5. Advantages & Disadvantages

### Advantages:

* **Clear Phase Separation:** Highly structured layout blocks make it explicitly clear where mounting initialization ends and cleanup code begins.

### Disadvantages:

* **Split Logic Bug risk:** Code related to a single feature gets scattered across different methods. For instance, setting up an interval happens in `componentDidMount`, but clearing it must happen in `componentWillUnmount`. This separation makes components harder to read and test compared to a single, unified `useEffect` hook.
* **Verbose Boilerplate:** Requires a class structure, constructors, calls to `super(props)`, and explicit manual function binding (`.bind(this)`).

---

## 6. Common Beginner Mistakes & Fixes

### Mistake 1: Infinite Update Loops inside `componentDidUpdate`

Executing `this.setState` directly inside `componentDidUpdate` without checking if the data actually changed:

```javascript
// ❌ WRONG
componentDidUpdate(prevProps, prevState) {
  this.setState({ userDetails: fetchFreshData() }); 
}

```

* **Why it's wrong:** `this.setState` triggers a re-render, which immediately calls `componentDidUpdate` again. This invokes `this.setState` *again*, throwing your browser into a freeze or crash loop.
* **The Fix:** Wrap your logic in a strict conditional statement comparing the previous values with the current values:

```javascript
// ✅ FIX
componentDidUpdate(prevProps, prevState) {
  if (prevState.userId !== this.state.userId) {
    // Only updates if the targeted data point actually shifted
    this.setState({ userDetails: fetchFreshData() });
  }
}

```

### Mistake 2: Forgetting to Clean Up Subscriptions

Setting an interval timer in `componentDidMount` but omitting `componentWillUnmount`.

* **Why it's wrong:** The background interval keeps executing even after the component is removed from the DOM, causing memory leaks and hidden console errors.
* **The Fix:** Always clear your timers or global listeners in `componentWillUnmount`.