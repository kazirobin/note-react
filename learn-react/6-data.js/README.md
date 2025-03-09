Creating a `data.js` file in React, importing the data, and displaying it conditionally is a common practice. Here's a step-by-step guide:

---

### Step 1: Create a `data.js` File
In your project, create a file named `data.js` to store the data you want to use.

#### Example `data.js`
```javascript
const data = [
  { id: 1, name: "Alice", age: 25, active: true },
  { id: 2, name: "Bob", age: 30, active: false },
  { id: 3, name: "Charlie", age: 35, active: true },
];

export default data;
```

Here, we export an array of objects, where each object contains information about a person.

---

### Step 2: Import `data.js` in a React Component
In your React component file (e.g., `App.js`), import the data.

#### Example: Importing Data
```javascript
import React from "react";
import data from "./data";

function App() {
  return (
    <div>
      <h1>User List</h1>
      {data.map((user) =>
        user.active ? (
          <p key={user.id}>
            {user.name} is active and {user.age} years old.
          </p>
        ) : (
          <p key={user.id}>{user.name} is inactive.</p>
        )
      )}
    </div>
  );
}

export default App;
```

---

### Step 3: Show Data Conditionally
In the `App` component:
1. Use the `.map()` method to loop through the `data` array.
2. Check a condition (e.g., `user.active`) for each object.
3. Render different content based on the condition.

#### Conditional Rendering
- **If the user is active (`active: true`)**, display their name and age.
- **If the user is inactive (`active: false`)**, display a message indicating they are inactive.

---

### Output in the Browser
For the example data:
```javascript
[
  { id: 1, name: "Alice", age: 25, active: true },
  { id: 2, name: "Bob", age: 30, active: false },
  { id: 3, name: "Charlie", age: 35, active: true },
]
```

The output will be:
```
User List
Alice is active and 25 years old.
Bob is inactive.
Charlie is active and 35 years old.
```

---

### Additional Enhancements
1. **Styling**: Add CSS classes for active and inactive users for better visualization.
   ```jsx
   <p key={user.id} className={user.active ? "active" : "inactive"}>
     {user.name} is {user.active ? `active and ${user.age} years old` : "inactive"}.
   </p>
   ```

2. **Filter Data**: If you want to show only active users:
   ```jsx
   {data.filter((user) => user.active).map((user) => (
     <p key={user.id}>
       {user.name} is active and {user.age} years old.
     </p>
   ))}
   ```

3. **Reusable Component**: Create a `User` component to display each user's information:
   ```jsx
   function User({ name, age, active }) {
     return (
       <p className={active ? "active" : "inactive"}>
         {name} is {active ? `active and ${age} years old` : "inactive"}.
       </p>
     );
   }
   
   function App() {
     return (
       <div>
         <h1>User List</h1>
         {data.map((user) => (
           <User key={user.id} {...user} />
         ))}
       </div>
     );
   }
   ```

This approach is modular and makes your code more maintainable.

[Back to Home](../../)

[1. extensions](../extensions/)

[2. setup-react](../2-setup-react/)

[3. children-props](../3-children-props/)

[4. children-props](../4-condition-to-show/)

5. data.js

[6. form](../6-form/)