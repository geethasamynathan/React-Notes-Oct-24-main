# Create React App
Create a new React app using create-react-app.

`npx create-react-app jwt-auth`

`npm install axios react-router-dom`

# Set Up Local Storage and Authentication in React
Create AuthContext.js to handle authentication:

```javascript
import React, { createContext, useState, useEffect } from "react";
const AuthContext = createContext();

export const AuthProvider = ({ children }) => {
  const [auth, setAuth] = useState(null);

  useEffect(() => {
    const token = localStorage.getItem("token");
    if (token) {
      setAuth(token);
    }
  }, []);

  const login = (token) => {
    setAuth(token);
    localStorage.setItem("token", token);
  };

  const logout = () => {
    setAuth(null);
    localStorage.removeItem("token");
  };

  return (
    <AuthContext.Provider value={{ auth, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
};

export default AuthContext;
```
# Create PrivateRoute.js to protect routes:
```javascript
import React, { useContext } from "react";
import { Outlet, Navigate } from "react-router-dom";
import AuthContext from "./AuthContext";

const PrivateRoute = () => {
  const { auth } = useContext(AuthContext);

  return auth ? <Outlet /> : <Navigate to="/login" />;
};

export default PrivateRoute;
```

# create services folder
### create Autuservices.js
```js
import axios from "axios";

const API_URL = "https://localhost:7299/api/Authentication";

const login = async (username, password) => {
  try {
    console.log("service called");
    console.log(username, password);
    const response = await axios.post(
      `https://localhost:7299/api/Authentication/login`,
      {
        username,
        password,
      }
    );

    console.log(`response ${response}`);
    const token = response.data.token;

    // Save the token in localStorage
    localStorage.setItem("token", token);

    return token;
  } catch (error) {
    console.error("Error logging in:", error);
    throw error;
  }
};

const getProtectedData = async () => {
  try {
    const token = localStorage.getItem("token");

    const response = await axios.get(
      "https://localhost:7299/api/protected-endpoint",
      {
        headers: {
          Authorization: `Bearer ${token}`,
        },
      }
    );

    return response.data;
  } catch (error) {
    console.error("Error fetching protected data:", error);
    throw error;
  }
};

export { login, getProtectedData };

```
## Create ProductService.js
```js
// src/Services/ProductService.js
import axios from "axios";

const API_URL = "https://localhost:7299/api/Products";

const getProducts = async () => {
  try {
    const token = localStorage.getItem("token");
    const response = await axios.get(API_URL, {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    });
    return response.data;
  } catch (error) {
    console.error("Error fetching products:", error);
    throw error;
  }
};

export { getProducts };

```

# Header.jsx
```javascript

import React, { useContext } from "react";
import { Link, useNavigate } from "react-router-dom";
import AuthContext from "./AuthContext";

const Header = () => {
  const { auth, logout } = useContext(AuthContext);
  const navigate = useNavigate();

  const handleLogout = () => {
    logout();
    localStorage.setItem("token", "");
    navigate("/login");
  };

  return (
    <header>
      <nav>
        <ul>
          <li>
            <Link to="/">Home</Link>
          </li>
          {!auth ? (
            <li>
              <Link to="/login">Login</Link>
            </li>
          ) : (
            <li>
              <button onClick={handleLogout}>Logout</button>
            </li>
          )}
          <li>
            <Link to="/register">Register</Link>
          </li>
          {auth && (
            <li>
              <Link to="/products">Products</Link>
            </li>
          )}
        </ul>
      </nav>
    </header>
  );
};

export default Header;
```

# Footer.jsx

```javascript
import React from "react";

const Footer = () => {
  return (
    <footer>
      <p>&copy; {new Date().getFullYear()} Your Company Name</p>
    </footer>
  );
};

export default Footer;

```
# Home.jsx
```javascript
import React from "react";

const Home = () => {
  return (
    <main>
      <h1>Welcome to Our Application</h1>
      <p>This is the main content area.</p>
    </main>
  );
};

export default Home;

```

# productList.jsx
```js
import React, { useEffect, useState } from "react";
import { getProducts } from "./Services/ProductService";
import "./ProductList.css";

const ProductList = () => {
  const [products, setProducts] = useState([]);

  useEffect(() => {
    const fetchProducts = async () => {
      try {
        const data = await getProducts();
        setProducts(data);
      } catch (error) {
        console.error("Failed to fetch products:", error);
      }
    };

    fetchProducts();
  }, []);

  return (
    <div className="product-list">
      {products.map((product) => (
        <div className="product-card" key={product.id}>
          <h2>{product.name}</h2>
          <img src={product.image} alt={product.name} />
          <p>{product.description}</p>
          <p>Price: ${product.price}</p>
          <button>Buy</button>
        </div>
      ))}
    </div>
  );
};

export default ProductList;
```

# prodcutList.css
```css
.product-list {
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
  }
  
  .product-card {
    border: 1px solid #ccc;
    border-radius: 5px;
    padding: 16px;
    margin: 16px;
    width: 300px;
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    text-align: center;
  }
  
  .product-card img {
    width: 100%;
    height: 350px;
    border-bottom: 1px solid #ccc;
  }
  
  .product-card h3 {
    margin: 16px 0;
  }
  
  .product-card button {
    background-color: #28a745;
    color: white;
    padding: 10px 16px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
  }
  
  .product-card button:hover {
    background-color: #218838;
  }
  
```

# index.html
`<link
      rel="stylesheet"
      href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css"
    />`


# for toastr 
`npm install react-toastify`
    # App.jsx
```javascript
    import logo from "./logo.svg";
import "./App.css";
import { BrowserRouter as Router, Routes, Route } from "react-router-dom";
import Login from "./Login";
import Header from "./Header";
import Footer from "./Footer";
import Register from "./Register";
import PrivateRoute from "./PrivateRoute";
import { AuthProvider } from "./AuthContext";
import { ToastContainer } from "react-toastify";
import "react-toastify/dist/ReactToastify.css";
import Products from "./Products";
import ProductList from "./ProductList";
import Home from "./Home ";

function App() {
  return (
    <Router>
      <AuthProvider>
        <Header />
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/login" element={<Login />} />
          <Route path="/register" element={<Register />} />
          <Route element={<PrivateRoute />}>
            <Route path="/products" element={<ProductList />} />
          </Route>
        </Routes>
        <Footer />
        <ToastContainer />
      </AuthProvider>
    </Router>
  );
}

export default App;
```

# App.css
```css
body {
  font-family: Arial, sans-serif;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

header, footer {
  background-color: #333;
  color: #fff;
  text-align: center;
  padding: 1rem;
}

header nav ul {
  list-style-type: none;
  padding: 0;
}

header nav ul li {
  display: inline;
  margin: 0 10px;
}

header nav ul li a {
  color: #fff;
  text-decoration: none;
}

main {
  padding: 2rem;
  text-align: center;
}
form 
{
   display: flex; 
   flex-direction: column;
   } 
.form-group 
{ 
  margin-bottom: 1rem;
   position: relative;
 } 
.form-group input { 
  width: 100%; padding: 0.75rem 1rem; 
  padding-left: 2.5rem; /* Adjust for icon */ 
  border: 1px solid #ccc; border-radius: 4px; 
} 
.form-group i { 
  position: absolute; 
  left: 1rem; top: 50%; 
  transform: translateY(-50%);
   color: #888; } 
   
   button { 
    padding: 0.75rem 1rem; 
    background-color: #28a745; border: none; 
    border-radius: 4px; color: #fff; 
    cursor: pointer;
     font-size: 1rem; 
    margin-top: 1rem; } 
    
button:hover { background-color: #218838;}
```
