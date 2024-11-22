
# example without using useState hook
```js
import React from "react";

export default function Counter() {
  let count = 120;
  const handleClick = () => {
    count++;
    console.log(count);
  };
  return (
    <>
      <p> Count variabe value is {count}</p>

      <button onClick={handleClick}> + </button>
    </>
  );
}

```


# simple Example for Conditional Rendering
```js
import React, { useState } from "react";

export default function LogintLogout() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [user, setUser] = useState(null);
    const handleLogin = () => {
      setIsLoggedIn(true);
      setUser({ name: "Hexaware" });
    };
    const handleLogout = () => {
      setIsLoggedIn(false);
      setUser(null);
    };
  return (
    <div>
      {isLoggedIn ? (
        <div>
          <h1>Welcome,{user.name}</h1>
          <button onClick={handleLogout}>Logout</button>
        </div>
      ) : (
        <div>
          <h1>Please Login</h1>
          <button onClick={handleLogin}>Login</button>
        </div>
      )}
    </div>    
  );
}

```
## Another way to do the same onClick inline arrow function

```js 
import React, { useState } from "react";

export default function LogintLogout() {
  const [isLoggedIn, setIsLoggedIn] = useState(false);
  const [user, setUser] = useState(null);
 return(
    <div>
      {isLoggedIn ? (
        <div>
          <h1>Welcome,{user.name}</h1>
          <button
            onClick={() => {
              setIsLoggedIn(false);
              setUser(null);
            }}
          >
            Logout
          </button>
        </div>
      ) : (
        <div>
          <h1>Please Login</h1>
          <button
            onClick={() => {
              setIsLoggedIn(true);
              setUser({ name: "to Hexaware" });
            }}
          >
            Login
          </button>
        </div>
      )}
    </div>
  );
}
```

