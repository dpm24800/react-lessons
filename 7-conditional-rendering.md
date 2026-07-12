# Chapter 7: Conditional Rendering

## 1. Concept Explanation

In a real-world web application, the user interface isn't static—it adapts dynamically based on different conditions. For instance, if a user is logged in, you show a "Log Out" button; if they aren't, you show "Log In" and "Sign Up" options. If data is still loading from an API, you display a spinning wheel loader; if the API fails, you display an error message.

In React, **Conditional Rendering** is the process of displaying different UI layouts based on specific conditions or state states.

Unlike some other frontend frameworks that use proprietary custom HTML attributes (like `v-if` or `*ngIf`), React relies entirely on **standard JavaScript logic structures** to control what gets rendered.

### The 3 Core Patterns of Conditional Rendering

1. **Standard `if/else` Statements:** Best used *outside* of your JSX return block when you want to return an entirely different component layout altogether.
2. **The Ternary Operator (`condition ? true : false`):** Best used *inside* your JSX return block when you want to alternate between two different elements inline.
3. **The Logical AND Operator (`condition && element`):** Best used *inside* your JSX return block when you want an element to render **only** if a condition is true, and render absolutely nothing if it is false (short-circuiting).

---

## 2. Code Blueprint

Let's look at a comprehensive component that utilizes all three primary conditional rendering strategies within a mock authentication status card.

```javascript
import React, { useState } from 'react';

function AuthenticationDashboard() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [isLoading, setIsLoading] = useState(false);
  const [hasError, setHasError] = useState(false);

  const toggleLogin = () => {
    setIsLoading(true);
    setHasError(false);
    
    // Simulating a brief network latency check
    setTimeout(() => {
      setIsLoading(false);
      setIsLoggedIn(prev => !prev);
    }, 800);
  };

  // PATTERN 1: Standard if/else Statement (Executed outside the core JSX return block)
  if (hasError) {
    return (
      <div className="error-panel">
        <h3>System Error</h3>
        <p>Unable to process authentication. Please try again later.</p>
        <button onClick={() => setHasError(false)}>Dismiss</button>
      </div>
    );
  }

  return (
    <div className="auth-card" style={{ padding: '20px', border: '1px solid #ddd' }}>
      <h2>Security Portal</h2>

      {/* PATTERN 2: Ternary Operator (condition ? elementIfTrue : elementIfFalse) */}
      {isLoading ? (
        <p className="loading-text">Verifying credentials... Please wait.</p>
      ) : (
        <p>Welcome, {isLoggedIn ? <strong>Authorized User</strong> : "Guest Node"}.</p>
      )}

      {/* PATTERN 3: Logical AND (&&) Short-Circuit Operator */}
      {/* This block renders ONLY if isLoggedIn is true */}
      {isLoggedIn && (
        <div className="secure-zone" style={{ background: '#e2f0d9', padding: '10px', margin: '10px 0' }}>
          🔒 <strong>Confidential Data Unlocked:</strong> Access token: <code>0x7F9B...</code>
        </div>
      )}

      <div className="control-actions" style={{ marginTop: '15px' }}>
        <button onClick={toggleLogin} disabled={isLoading}>
          {isLoggedIn ? "Log Out" : "Log In"}
        </button>

        {/* Simulating an instantaneous system crash toggle */}
        <button onClick={() => setHasError(true)} style={{ marginLeft: '10px', background: '#f8cbad' }}>
          Simulate Crash Error
        </button>
      </div>
    </div>
  );
}

export default AuthenticationDashboard;

```

---

## 3. Real-World Use Cases

* **Asynchronous Data State Handlers:** Displaying a skeleton shimmer box while waiting for an API response, transitioning to a data layout table when loaded, or revealing a critical warning alert if the fetch breaks.
* **Feature Flag Switches:** Displaying or completely hiding specific administrative premium features, buttons, or dashboards depending on the user's active sub-tier ranking.

---

## 4. When to Use vs. When NOT to Use

### When to Use:

* Use **ternary operators** for simple inline switching between two elements (e.g., swapping a Moon icon with a Sun icon for Dark/Light mode).
* Use **Logical AND (`&&`)** when you want an element to either show up or completely vanish from the page structure with no alternative fallback.
* Use **`if/else` returns** when the entire layout structure changes fundamentally based on a condition (e.g., displaying an entire login splash screen vs. displaying the main dashboard app).

### When NOT to Use:

* **Complex Multi-Branch Nesting:** Avoid nesting ternary expressions inside other ternary expressions (e.g., `condition ? (subCondition ? A : B) : C`). This creates unreadable code that is incredibly difficult to debug. For complex multi-layered rendering conditions, move the logic *above* your JSX return block and use standard JavaScript `switch` statements or `if/else` blocks to store the target layout in a variable.

---

## 5. Advantages & Disadvantages

### Advantages:

* **No Proprietary Syntax:** Relies entirely on native JavaScript logic primitives that developers already understand.
* **Highly Dynamic UIs:** Allows interfaces to rapidly alter their entire spatial configuration instantly in direct synchronization with changing state parameters.
* **Clean DOM Structure:** Unrendered components are completely removed from the DOM tree, rather than just hidden with CSS styles (like `display: none`), preserving browser memory and improving performance.

### Disadvantages:

* **Readability Deterioration:** Interleaving heavy logical conditions directly within extensive HTML markup tags can quickly make JSX components messy and challenging to visually scan.

---

## 6. Common Beginner Mistakes & Fixes

### Mistake 1: Accidental Numeric Rendering with the `&&` Operator

Using a number property as a evaluation shorthand check inside a list wrapper layout:

```javascript
// ❌ WRONG
const [items, setItems] = useState([]); // Empty array (length is 0)

return (
  <div>
    {items.length && <p>Displaying {items.length} notifications!</p>}
  </div>
);

```

* **The Visual Bug:** Instead of rendering nothing, your browser physically draws the number `0` directly onto the webpage surface.
* **Why it's wrong:** JavaScript treats the number `0` as a falsy value. When using the `&&` operator, if the left side evaluates to a falsy *value*, JavaScript short-circuits and returns that *exact value* (`0`). Since `0` is a valid number, React renders it on screen.
* **The Fix:** Ensure your left-side comparison condition explicitly resolves into a pure **boolean** (`true` or `false`):

```javascript
//  FIX
return (
  <div>
    {items.length > 0 && <p>Displaying {items.length} notifications!</p>}
  </div>
);

```

### Mistake 2: Forgetting to Return Elements in `if/else` Blocks

```javascript
// ❌ WRONG
if (!isUserVerified) {
  <div className="warning">Please verify your account!</div>
}

```

* **Why it's wrong:** The code creates a JSX element but doesn't pass it back to React. The function will continue running and hit the default return block below, ignoring the condition entirely.
* **The Fix:** You must explicitly prepend the **`return`** keyword to halt the component execution and deliver the UI fragment back out:

```javascript
//  FIX
if (!isUserVerified) {
  return <div className="warning">Please verify your account!</div>;
}

```