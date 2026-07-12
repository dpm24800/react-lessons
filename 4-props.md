# Chapter 4: Props (Properties)

## 1. Concept Explanation

In React, components are designed to be reusable layout pieces. However, a reusable component is only helpful if you can pass different data into it. For example, if you have a `Button` component, you want to be able to change its label, color, or behavior depending on where it is used. This is where **Props** (short for properties) come in.

### What are Props?

Props are configuration parameters passed into a component by its parent, identical to how you pass arguments into a standard JavaScript function or attributes into an HTML tag.

### The Golden Rule: Props are Immutability (Read-Only)

The most important rule regarding props is that **they are strictly read-only**. A component must never modify its own props.

* **Unidirectional Data Flow:** Data in React flows in a single direction—downwards from parent components to child components.
* If a component needs to change a value over time due to user interaction, it should use **State** (which we cover in Chapter 5), *not* props.

### Default Props

Sometimes, a component expects a prop but doesn't receive one. To prevent your app from crashing or looking empty, you can assign **Default Props** to serve as a fallback.

---

## 2. Code Blueprint

Let's look at how a parent component (`App`) passes custom data down into a reusable child component (`UserCard`), complete with modern destructuring and default fallback values.

### The Child Component (`src/components/UserCard.js`)

```javascript
import React from 'react';

// Modern best practice: Destructure props directly inside the function parameters
function UserCard({ name, role, status = "Offline" }) {
  return (
    <div className={`user-card ${status.toLowerCase()}`}>
      <h2>{name}</h2>
      <p><strong>Role:</strong> {role}</p>
      <p><strong>Status:</strong> {status}</p>
    </div>
  );
}

// Legacy fallback alternative if default parameters aren't used:
// UserCard.defaultProps = { status: "Offline" };

export default UserCard;

```

### The Parent Component (`src/App.js`)

```javascript
import React from 'react';
import UserCard from './components/UserCard';

function App() {
  return (
    <div className="app-container">
      <h1>Team Directory</h1>
      
      {/* Passing strings, numbers, or expressions down as custom attributes */}
      <UserCard 
        name="Sarah Connor" 
        role="Security Architect" 
        status="Active" 
      />
      
      <UserCard 
        name="John Doe" 
        role="Junior Developer" 
        status="Away" 
      />
      
      {/* This component will fall back to its default status prop ("Offline") */}
      <UserCard 
        name="James Smith" 
        role="DevOps Engineer" 
      />
    </div>
  );
}

export default App;

```

---

## 3. Real-World Use Cases

* **UI Component Libraries (e.g., buttons, inputs, alerts):** Passing variations like `variant="primary"` or `size="large"` to alter a button's visual presentation dynamically.
* **Rendering Dynamic Lists:** Passing distinct database object entries (like blog post titles, product prices, or image URLs) down into individual structural cards inside a `.map()` loop layout.

---

## 4. When to Use vs. When NOT to Use

### When to Use:

* Use props whenever you need to pass static configuration values, dynamic data variables, or event handling functions down from a parent component to a child layout.

### When NOT to Use:

* **Deeply Nested Global States:** Avoid passing the exact same prop down through 5 or 10 layers of child components just to reach a single element at the bottom. This structural headache is called **Prop Drilling**. For global configurations (like user authentication, language preferences, or UI color themes), use the `useContext` Hook instead.
* **Mutable Component Data:** If a variable needs to be updated directly by the component itself (like tracking a checkbox check or updating text input characters), use **State**.

---

## 5. Advantages & Disadvantages

### Advantages:

* **Enables Infinite Reusability:** Write your layout framework code once and reuse it across hundreds of custom instances just by shifting the data inputs.
* **Predictable Architecture:** Because props are read-only and flow strictly downward, debugging where an application's data originated is highly straightforward.
* **Component Decoupling:** Isolates visual code blocks from your core business logic layer, making your application significantly easier to test.

### Disadvantages:

* **Prop Drilling Pitfalls:** In mid-to-large scale projects, tracking data flowing through layers of intermediate components that don't even use the data themselves can rapidly clutter your codebase.

---

## 6. Common Beginner Mistakes & Fixes

### Mistake 1: Attempting to Reassign Prop Values Directly

Trying to change a prop value inside a child component:

```javascript
// ❌ WRONG
function ProfileCard(props) {
  props.name = "New Name"; // Modifying props directly
  return <h1>{props.name}</h1>;
}

```

* **The Error Message:** `TypeError: Cannot assign to read only property 'name' of object '#<Object>'`
* **The Fix:** If you need to modify data locally, look toward initializing a local state item or have the parent component pass down an update handler function to change the source value safely.

### Mistake 2: Forgetting Curly Braces when Destructuring

```javascript
// ❌ WRONG
function UserProfile(name, role) { 
  // React thinks the first argument 'name' is the entire props object!
  return <h1>{name}</h1>;
}

```

* **Why it's wrong:** React passes *one single object* (the `props` object) as the first argument to your component function. If you write `(name, role)`, the first parameter becomes the whole object, and the second parameter remains undefined.
* **The Fix:** Wrap your custom parameter attributes inside structural curly braces to extract them correctly from the wrapper object:

```javascript
//  FIX
function UserProfile({ name, role }) {
  return <h1>{name}</h1>;
}

```