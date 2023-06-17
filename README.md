# Forms
#### Handle multiple input field
```javascript
import React, { useState } from 'react';

const Form = () => {
    const [user, setUser] = useState({name: '', email: '', password: ''});

    const handleChange = (e) => {
        setUser({...user, [e.target.name]: e.target.value});
    }

    const handleSubmit = (e) => {
        e.preventDefault();
        console.log(user);
        setUser({name: '', email: '', password: ''});
    }

    return (
        <form onSubmit={handleSubmit}>
            <input type="text" name="name" value={user.name} onChange={handleChange} placeholder="Your name" required />
            <input type="email" name="email" value={user.email} onChange={handleChange} placeholder="Your email" required />
            <input type="password" name="password" value={user.password} onChange={handleChange} placeholder="Your password" required />
            <button type="submit">Submit</button>
        </form>
    );
}

export default Form;
```

##### Form input validation
```javascript
const validateName = (name) => {
    const regex = /^[A-Za-z]+(?:\s+[A-Za-z]+)*$/g;
    return regex.test(name);
}

const validateEmail = (email) => {
    const regex = /^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/g;
    return regex.test(email);
}

const validatePassword = (password) => {
    return password.length > 3;
}
```

##### Password strength checker
```javascript
const checkPasswordStrength = (password) => {
    const len = password.length;

    // uppercase, lowercase, [4, 6]
    const weak = /^(?=.*[a-zA-Z]).{4,6}$/g;

    // at least 1 uppercase, 1 lowercase, [4, 8]
    const fair = /^(?=.*[a-z])(?=.*[A-Z]).{4,8}$/g;

    // at least 1 uppercase, 1 lowercase, 1 digit, [6, 10]
    const good = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{6,10}$/g;

    // at least 1 uppercase, 1 lowercase, 1 digit, 1 special character [6,12]
    const strong = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&]).{6,12}$/g;

    // at least 1 uppercase, 1 lowercase, 1 digit, 1 special character [12,]
    const excellent = /^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[@$!%*?&])[A-Za-z\d@$!%*?&^()_\-=[\]{};':"\\|,.<>/?`~]{12,}$/g;

    if (len > 0 && len < 4) {
        setPasswordStrength(1);
    }
    if (weak.test(password)) {
        setPasswordStrength(2);
    }
    if (fair.test(password)) {
        setPasswordStrength(3);
    }
    if (good.test(password)) {
        setPasswordStrength(4);
    }
    if (strong.test(password)) {
        setPasswordStrength(5);
    }
    if (excellent.test(password)) {
        setPasswordStrength(6);
    }
    if (password.length === 0) {
        setPasswordStrength(0);
    }
}
```

# Hooks

## useState
- Used to track state in a function component
- Ex - const [current state, function that updated the state] = useState(initial state)
#### Counter app
```javascript
import React, { useState } from 'react';

const Counter = () => {
    const [count, setCount] = useState(0);

    return (
        <>
            <h1>Count: {count}</h1>
            <button onClick={() => setCount((prev) => prev + 1)}>Inc</button>
            <button onClick={() => setCount((prev) => prev - 1)}>Dec</button>
        </>
    );
}

export default Counter;
```

## useEffect
- Used to perform side effects in a component
- Ex - Fetching data, Directly updating DOM, Timers etc.
#### Timer
```javascript
import React, { useEffect, useState } from 'react';

const Timer = () => {
    const [timer, setTimer] = useState(0);

    useEffect(() => {
        const timerId = setInterval(() => {
            setTimer((prev) => prev + 1);
        }, 1000);

        return () => {
            clearInterval(timerId);
        }
    }, []);

    return (
        <div>
            <h1>Time count: {timer} Sec</h1>
        </div>
    );
}

export default Timer;
```

## useContext
- A way to manage state globally
- Used to share state between deeply nested component more easily
#### Provider
```javascript
import React, { createContext, useState } from 'react';

export const UserContext = createContext();

const UserContextProvider = ({ children }) => {
    const [user, setUser] = useState({name: 'Jhon', age: 24});

    return (
        <UserContext.Provider value={{user, setUser}}>
            {children}
        </UserContext.Provider>
    );
}

export default UserContextProvider;
```
#### index.js
```javascript
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import UserContextProvider from './UserContextProvider';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <UserContextProvider>
      <App />
    </UserContextProvider>
  </React.StrictMode>
);
```
#### Child
```javascript
import React, { useContext } from 'react';
import { UserContext } from './UserContextProvider';

const Child = () => {
    const { user } = useContext(UserContext);

    return (
        <div>
            <h1>Name: {user.name}</h1>
            <h1>Age: {user.age}</h1>
        </div>
    );
}

export default Child;
```

## useRef
- Used to access DOM element directly
- Store a mutable value that does not cause a re-render when updated
#### Render count
```javascript
import React, { useState, useEffect, useRef } from 'react';

const RenderCount = () => {
    const [input, setInput] = useState('');
    const counter = useRef(0);

    useEffect(() => {
        counter.current = counter.current + 1;
    }, [input]);

    const handleChange = (e) => {
        setInput(e.target.value);
    }

    return (
        <div>
            <input type="text" value={input} onChange={handleChange} placeholder="Enter text" />
            <p>Input value: {input}</p>
            <p>Render count: {counter.current}</p>
        </div>
    );
}

export default RenderCount;
```

#### Update DOM
```javascript
import React, { useState, useRef } from 'react';

const UpdateDOM = () => {
    const [boxShape, setBoxShape] = useState('Square');
    const elemRef = useRef();

    const handleClick = () => {
        if (boxShape === 'Square') {
            elemRef.current.style.width = '400px';
            setBoxShape('Rectangle');
        } else {
            elemRef.current.style.width = '200px';
            setBoxShape('Square');
        }
    }

    return (
        <div style={{display: 'flex', flexDirection: 'column', height: '100vh', alignItems: 'center', justifyContent: 'center'}}>
            <div onClick={handleClick} ref={elemRef} style={{display: 'flex', height: '200px', width: '200px', alignItems: 'center', justifyContent: 'center', background: '#222'}}>
                <p style={{color: '#fff', fontSize: '1.2rem'}}>Click Me!</p>
            </div>
            <p style={{paddingTop: '1rem'}}>This is a {boxShape}!</p>
        </div>

    );
}

export default UpdateDOM;
```

## useReducer
- Similar to useState Hook
- Used to keeping track of multiple pieces of state that relay on complex logic
#### Counter app
```javascript
import React, { useReducer } from 'react';

const initialState = {counter: 0};

const ACTIONS = {
    INCREASE: 'increase',
    DECREASE: 'decrease'
}

const reducer = (state, action) => {
    switch(action.type) {
        case ACTIONS.INCREASE:
            return {counter: state.counter + 1};
        
        case ACTIONS.DECREASE:
            return {counter: state.counter - 1};

        default:
            return state;
    }
}

const Counter = () => {
    const [state, dispatch] = useReducer(reducer, initialState);

    const handleIncrease = () => {
        dispatch({ type: ACTIONS.INCREASE });
    }

    const handleDecrease = () => {
        dispatch({ type: ACTIONS.DECREASE });
    }

    return (
        <div>
            <p>Counter: {state.counter}</p>
            <button onClick={handleIncrease}>Increment</button>
            <button onClick={handleDecrease}>Decrement</button>
        </div>
    );
}

export default Counter;
```

## useCallback
- Allows to isolate resource intensive functions so that they will not automatically run on every render
- Returns a memoized callback function
#### Calling function from inside useEffect
```javascript
import React, { useCallback, useEffect, useState } from 'react';

const MyApp = () => {
    const [users, setUsers] = useState(null);
    const [loader, setLoader] = useState(false);
    const [error, setError] = useState(null);

    const fetchData = useCallback(async (url) => {
        setLoader(true);
        try {
            const res = await fetch(url);
            const data = await res.json();
            setUsers(data);
            setLoader(false);
        } catch(err) {
            setLoader(false);
            setError(err);
        }
    }, []);

    useEffect(() => {
        fetchData('https://jsonplaceholder.typicode.com/users');
    }, [fetchData]);

    return (
        <div>
            {
                !loader ? error ? <p>Upps! Something went wrong!</p> 
                    : users && users.map((user) => {
                    return <div key={user.id}>
                        <p><strong>id:</strong> {user.id}</p>
                        <p><strong>name:</strong> {user.name}</p>
                        <p><strong>email:</strong> {user.email}</p>
                        <br />
                    </div>
                }) : <p>loading...</p>
            }
        </div>
    );
}

export default MyApp;
```

## useMemo
- Used to keep expensive or resource intensive functions from needlessly running
- Returns a memoized value
#### Improve app performance
```javascript
import React, { useState, useMemo } from 'react';

const expensiveCalculation = (value) => {
    for (let i = 0; i < 1000000000; i ++) {}
    return value;
}

const MyApp = () => {
    const [todo, setTodo] = useState([]);
    const [count, setCount] = useState(0);

    const addTodo = () => {
        setTodo([...todo, 'todo item']);
    }

    const calculation = useMemo(() => {
        return expensiveCalculation(count);
    }, [count]);

    return (
        <div>
            {todo.map((elem, index) => {
                return <p>{elem} {index + 1}</p>
            })}
            <button onClick={addTodo}>Add todo</button>

            <p>{calculation}</p>
            <button onClick={() => setCount(count + 1)}>Increment</button>
            <button onClick={() => setCount(count - 1)}>Decrement</button>
        </div>
    );
}

export default MyApp;
```

## Custom Hooks
- A mechanism to reuse stateful logic
#### A custom hook for fetching data
```javascript
import { useState, useEffect } from 'react';

const useFetch = (url) => {
    const [data, setData] = useState(null);
    const [isLoading, setIsLoading] = useState(false);
    const [error, setError] = useState(null);

    useEffect(() => {
        const fetchData = async () => {
            setIsLoading(true);
            try {
                const res = await fetch(url);
                const data = await res.json();
                setData(data);
            } catch (error) {
                setError(error);
            }
            setIsLoading(false);
        }

        fetchData();
    }, [url]);

    return { data, isLoading, error };
}

export default useFetch;
```

#### How to use custom hook in component
```javascript
import React from 'react';
import useFetch from './useFetch';
import './App.css';

const App = () => {
  const { data, isLoading, error } = useFetch('https://jsonplaceholder.typicode.com/users');

  return (
    <div>
        {!isLoading ? error ? <p>Upps! Something Wrong!!</p> :
            data && data.map((user) => {
                return <div>
                  <p>{user.name}</p>
                  <p>{user.email}</p>
                  <br />
                </div>
            }) : <p>Please wait...</p>
        }
    </div>
  );
}

export default App;
```

# Modals
## Modal using React Portals
#### index.html
```html
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="utf-8" />
  <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <meta name="description" content="Web site created using create-react-app" />
  <title>React App</title>
</head>

<body>
  <div id="root"></div>
  <div id="modal"></div>
</body>

</html>
```

#### Modal.jsx
```javascript
import React from 'react';
import ReactDOM from 'react-dom'
import { useNavigate, useParams } from 'react-router-dom';

const Modal = () => {
    const navigate = useNavigate();
    const { id } = useParams();

    const closeModal = () => {
        navigate('..');
    }

    return ReactDOM.createPortal(
        <div onClick={closeModal} style={{position: 'fixed', top: 0, left: 0, height: '100%', width: '100%', display: 'flex', justifyContent: 'center', alignItems: 'center', background: 'black'}}>
            <div onClick={(e) => e.stopPropagation()} style={{color: '#fff', width: '600px', height: '500px', background: 'red'}}>Modal {id}</div>
        </div>,
        document.getElementById('modal')
    );
}

export default Modal;
```

#### App.js
```javascript
import React from 'react';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './components/Home';
import Modal from './components/Modal';
import './App.css';

const App = () => {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/:id" element={<Modal />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

# Search, Filter & Pagination
#### data.js
```javascript
let arr = [];

for (let i = 1; i <= 10000; i ++) {
    let gender = ['male', 'female'];
    let category = ['adult', 'child'];
    let country = ['Bangladesh', 'Maldives', 'Thailand', 'Africa', 'Singapur', 'Malaysia', 'USA', 'Canada', 'England', 'Egypt'];
    
    let obj = {
        id: i,
        username: `user${i}`,
        email: `mail${i}@examp.com`,
        phone: Math.floor(Math.random() * 9000000000 + 1000000000),
        gender: gender[Math.floor(Math.random() * 2)],
        category: category[Math.floor(Math.random() * 2)],
        country: country[Math.floor(Math.random() * 10)]
    }
    
    arr.push(obj);
}

export {arr};
```

#### MyApp.jsx
```javascript
import React, { useEffect, useState } from 'react';

// components
import Footer from './Footer';
import Pagination from './Pagination';

// data
import { arr } from '../data';

const MyApp = () => {
    const [data, setData] = useState('');
    const [filteredData, setFilteredData] = useState([]);
    const [searchText, setSearchText] = useState('');
    const [searchTimer, setSearchTimer] = useState();
    const [selectedCountry, setSelectedCountry] = useState('all');
    const [loader, setLoader] = useState(false);

    // Pagination
    const [currentPage, setCurrentPage] = useState(1);
    const [itemsPerPage, setItemsPerPage] = useState(10);

    // search and filter
    useEffect(() => {
        setData(arr);

        const filterSearchItems = (item) => {
            let mainTxt = ''.concat(item.id, item.username, item.email, item.phone, item.gender, item.category, item.country).replace(/[^a-zA-Z0-9@]/g, '').toLowerCase();
            let srchTxt = searchText.replace(/[^a-zA-Z0-9@]/g, '').toLowerCase();
    
            if (searchText === '' || mainTxt.includes(srchTxt)) {
                return true;
            } else {
                return false;
            }
        }

        let filteredDataItems = [];

        filteredDataItems = data && data.filter((item) => {
            if (selectedCountry === 'all' && filterSearchItems(item)) {
                return item;
            } else if (item.country.toLowerCase().includes(selectedCountry) && filterSearchItems(item)) {
                return item;
            } else {
                return null;
            }
        });

        setFilteredData(filteredDataItems);

    }, [data, searchText, selectedCountry]);

    const handleSelectedCountry = (e) => {
        setSelectedCountry(e.target.value);
    }

    const handleSearchText = (e) => {
        clearTimeout(searchTimer);
        setLoader(true);
        setSearchTimer(
            setTimeout(() => {
                setSearchText(e.target.value);
                setLoader(false);
            }, 800)
        );
    }

    let lastIndex = currentPage * itemsPerPage;
    let firstIndex = lastIndex - itemsPerPage;

    return (
        <>
            <div className="container">
                <div className="topbar">
                    <div className="category">
                        <select id="selectCountry" value={selectedCountry} onChange={handleSelectedCountry}>
                            <option value="all">Country (All)</option>
                            <option value="bangladesh">Bangladesh</option>
                            <option value="maldives">Maldives</option>
                            <option value="thailand">Thailand</option>
                            <option value="africa">Africa</option>
                            <option value="singapur">Singapur</option>
                            <option value="malaysia">Malaysia</option>
                            <option value="usa">USA</option>
                            <option value="canada">Canada</option>
                            <option value="england">England</option>
                            <option value="egypt">Egypt</option>
                        </select>
                    </div>

                    <div className="searchBox">
                        <input type="text" placeholder="Search..." onChange={handleSearchText} />
                    </div>
                    <div className="dataCountBox">
                        <p>Items: {filteredData.length}/{data.length}</p>
                    </div>
                </div>

                <div className="dataContent">
                    <table className="dataTable">
                        <thead>
                            <tr>
                                <th>id</th>
                                <th>username</th>
                                <th>email</th>
                                <th>phone</th>
                                <th>gender</th>
                                <th>category</th>
                                <th>location</th>
                            </tr>
                        </thead>
                        <tbody>
                            {loader ? <tr style={{textAlign: 'left'}}><td style={{paddingTop: '0.4rem'}}>Searching...</td></tr> : null}
                            {!loader && ((filteredData.length) ? filteredData.slice(firstIndex, lastIndex).map((elem, index) => {
                                return <tr key={index}>
                                    <td>{elem.id}</td>
                                    <td>{elem.username}</td>
                                    <td>{elem.email}</td>
                                    <td>{elem.phone}</td>
                                    <td>{elem.gender}</td>
                                    <td>{elem.category}</td>
                                    <td>{elem.country}</td>
                                </tr>
                            }) :
                            <tr style={{textAlign: 'left'}}><td style={{paddingTop: '0.4rem'}}>No item found!</td></tr>)}
                        </tbody>
                    </table>
                </div>

                <Pagination currentPage={currentPage} setCurrentPage={setCurrentPage} itemsPerPage={itemsPerPage} setItemsPerPage={setItemsPerPage} totalItems={filteredData.length} />
            </div>
            <Footer />
        </>
    );
}

export default MyApp;
```

#### Pagination.jsx
```javascript
import React, { useState, useEffect } from 'react';
import { MdKeyboardDoubleArrowLeft } from 'react-icons/md';
import { MdKeyboardDoubleArrowRight } from 'react-icons/md';

const Pagination = ({ currentPage, setCurrentPage, itemsPerPage, setItemsPerPage, totalItems }) => {
    const [totalPages, setTotalPages] = useState(100);

    useEffect(() => {
        setTotalPages(Math.ceil(totalItems / itemsPerPage));
        setCurrentPage(1);
    }, [itemsPerPage, totalItems, setCurrentPage]);

    const nextPage = () => {
        if (currentPage === totalPages) {
            return null;
        } else {
            setCurrentPage((prev) => prev + 1);
        }
    }

    const prevPage = () => {
        if (currentPage === 1) {
            return null;
        } else {
            setCurrentPage((prev) => prev - 1);
        }
    }

    const multiNextPage = () => {
        if (currentPage + 3 >= totalPages) {
            setCurrentPage(totalPages - 1);
        } else {
            setCurrentPage(currentPage + 3);
        }
    }

    const multiPrevPage = () => {
        if (currentPage - 3 <= 1) {
            setCurrentPage(1);
        } else {
            setCurrentPage(currentPage - 2);
        }
    }

    const handleItemsPerPage = (e) => {
        setItemsPerPage(e.target.value);
    }

    return (
        <div className="paginationBox">
            <ul className="paginationBtns">
                <li className="paginationBtn"><button className="arrowBtn" onClick={prevPage}><MdKeyboardDoubleArrowLeft /></button></li>
                {(currentPage > 2) && <li className="paginationBtn"><button onClick={multiPrevPage}>...</button></li>}
                {(currentPage > 1) && <li className="paginationBtn"><button onClick={prevPage}>{currentPage - 1}</button></li>}
                <li className="paginationBtn"><button disabled className="activeBtn">{currentPage}</button></li>
                {(currentPage < totalPages) && <li className="paginationBtn"><button onClick={nextPage}>{currentPage + 1}</button></li>}
                {(currentPage + 2 < totalPages) && <li className="paginationBtn"><button onClick={multiNextPage}>...</button></li>}
                {(currentPage + 1 < totalPages) && <li className="paginationBtn"><button onClick={() => setCurrentPage(totalPages)}>{totalPages}</button></li>}                
                <li className="paginationBtn"><button className="arrowBtn" onClick={nextPage}><MdKeyboardDoubleArrowRight /></button></li>
            </ul>

            <label className="itemsPerPage" htmlFor="itemsPerPage">
                <span>Items Per Page</span>
                <select value={itemsPerPage} onChange={handleItemsPerPage} name="itemsPerPage" id="itemsPerPage">
                    <option value="10">10</option>
                    <option value="20">20</option>
                    <option value="30">30</option>
                    <option value="50">50</option>
                    <option value="80">80</option>
                    <option value="100">100</option>
                </select>
            </label>
        </div>
    );
}

export default Pagination;
```

#### Style for search, filter and pagination
```css
* {
  margin: 0;
  padding: 0;
}

.container {
  margin: 0 auto;
  max-width: 1200px;
  width: 100%;
  height: auto;
  min-height: calc(100vh - 4rem);
}

.topbar {
  margin-top: 4rem;
  padding: 1rem 0;
  display: flex;
  height: auto;
  width: 100%;
  align-items: center;
  justify-content: space-between;
}

#selectCountry {
  padding: 0.2rem 0.4rem;
  color: #111;
  font-size: 1rem;
  border: 1px solid #888;
  outline: none;
  border-radius: 0.2rem;
}

.searchBox {
  width: 300px;
}

.searchBox input {
  padding: 0.2rem 0.6rem;
  color: #111;
  font-size: 1rem;
  width: 100%;
  border: 1px solid #888;
  outline: none;
  border-radius: 0.2rem;
  box-sizing: border-box;
}

.dataCountBox p {
  padding: 0.2rem 0.6rem;
  color: #111;
  font-size: 1rem;
}

.dataContent {
  margin-top: 1rem;
}

.dataTable {
  margin-bottom: 2rem;
  width: 100%;
  border-collapse: collapse;
}

thead {
  color: #fff;
  background: steelblue;
}

tr {
  text-align: center;
}

tr:nth-child(even) {
  background: #ddd;
}

th {
  padding: 0.4rem 0.2rem;
}

td {
  padding: 0.4rem 0.2rem;
}





/* pagination */
.paginationBox {
  margin-top: 1rem;
  display: flex;
  height: auto;
  width: 100%;
  max-width: 1200px;
  align-items: center;
  justify-content: space-between;
}

.paginationBtns {
  width: auto;
  display: flex;
  justify-content: flex-start;
  align-items: center;
  list-style-type: none;
}

.paginationBtn {
  margin-right: 1rem;
}

.paginationBtn button {
  padding: 0.3rem 0.6rem;
  display: flex;
  align-items: center;
  justify-content: center;
  border: none;
  outline: none;
  background: #ccc;
  border-radius: 0.1rem;
  cursor: pointer;
}

.paginationBtn button.arrowBtn {
  padding: 0.42rem 0.6rem;
}

.paginationBtn button.activeBtn {
  color: #fff;
  background: steelblue;
}

.itemsPerPage {
  display: flex;
  align-items: center;
}

.itemsPerPage span {
  margin-right: 0.6rem;
}

.itemsPerPage select {
  padding: 0.2rem 0.4rem;
  color: #111;
  font-size: 1rem;
  border: 1px solid #888;
  outline: none;
  border-radius: 0.2rem;
}
```

# React Router
#### App.js
```javascript
import React, { useContext } from 'react';
import { BrowserRouter, Routes, Route, Navigate } from 'react-router-dom';
import { LoginContext } from './context/LoginContextProvider';
import { CartContext } from './context/CartContextProvider';
import './App.scss';

// pages
import Home from './pages/Home';
import About from './pages/About';
import Category from './pages/Category';
import Products from './pages/Products';
import Contact from './pages/Contact';
import Error404 from './pages/Error404';

// auth
import Login from './auth/Login';
import Signup from './auth/Signup';
import AdminLogin from './auth/AdminLogin';
import CreateAdmin from './auth/CreateAdmin';

// user profile
import Profile from './pages/user/Profile';
import UpdateProfile from './pages/user/UpdateProfile';
import UserDashboard from './pages/user/UserDashboard';
import MyOrders from './pages/user/MyOrders';
import OrderDetails from './pages/user/OrderDetails';

// shopping cart
import Cart from './pages/cart/Cart';
import Checkout from './pages/cart/Checkout';
import Payment from './pages/cart/Payment';
import Review from './pages/cart/Review';
import EmptyCart from './pages/cart/EmptyCart';

// admin dashboard
import AdminDashboard from './pages/admin/AdminDashboard';
import AdminProducts from './pages/admin/AdminProducts';
import UploadProducts from './pages/admin/UploadProducts';
import AdminProductDetails from './pages/admin/AdminProductDetails';
import AdminOrders from './pages/admin/AdminOrders';
import AdminOrderDetails from './pages/admin/AdminOrderDetails';
import AdminUsers from './pages/admin/AdminUsers';
import AdminUserDetails from './pages/admin/AdminUserDetails';
import AdminProfile from './pages/admin/AdminProfile';
import AdminUpdateProfile from './pages/admin/AdminUpdateProfile';

import ScrollToTop from './effects/ScrollToTop';

const App = () => {
  const { currentUser, currentAdmin } = useContext(LoginContext);
  const { cartItems, checkoutFormFilled, orderConfirmed } = useContext(CartContext);
    
  // protected routes
  const RequireAuth = ({children}) => {
    return currentUser ? (children) : <Navigate to="/user/login" replace />
  }

  const RequireAdminAuth = ({children}) => {
    return currentAdmin ? (children) : <Navigate to="/admin/login" replace />
  }

  const RequireCart = ({children}) => {
    return cartItems.length >= 1 ? (children) : <Navigate to="/empty-cart" replace />
  }

  const RequirePayment = ({children}) => {
    return checkoutFormFilled ? (children) : <Navigate to="/checkout" replace />
  }

  const RequireCartReview = ({children}) => {
    return orderConfirmed ? (children) : <Navigate to="/payment" replace />
  }

  return (
      <BrowserRouter>
        <ScrollToTop />
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          <Route path="/products" element={<Products />} />
          <Route path="/products/:category" element={<Category />} />
          <Route path="/contact" element={<Contact />} />

          <Route path="/cart" element={<RequireCart><Cart/></RequireCart>} />
          <Route path="/checkout" element={<RequireAuth><Checkout/></RequireAuth>} />
          <Route path="/payment" element={<RequirePayment><Payment/></RequirePayment>} />
          <Route path="/review" element={<RequireCartReview><Review/></RequireCartReview>} />
          <Route path="/empty-cart" element={<EmptyCart/>} />

          <Route path="/user/login" element={<Login />} />
          <Route path="/user/signup" element={<Signup />} />
          <Route path="/admin/login" element={<AdminLogin />} />
          <Route path="/admin/create" element={<CreateAdmin />} />

          <Route path="/user/profile" element={<RequireAuth><Profile /></RequireAuth>} />
          <Route path="/user/update-profile" element={<RequireAuth><UpdateProfile /></RequireAuth>} />
          <Route path="/user/dashboard" element={<RequireAuth><UserDashboard /></RequireAuth>} />
          <Route path="/user/orders" element={<RequireAuth><MyOrders /></RequireAuth>} />
          <Route path="/user/orders/:id" element={<RequireAuth><OrderDetails /></RequireAuth>} />
          
          <Route path="/admin/dashboard" element={<RequireAdminAuth><AdminDashboard /></RequireAdminAuth>} />
          <Route path="/admin/users" element={<RequireAdminAuth><AdminUsers /></RequireAdminAuth>} />
          <Route path="/admin/users/:id" element={<RequireAdminAuth><AdminUserDetails /></RequireAdminAuth>} />
          <Route path="/admin/products" element={<RequireAdminAuth><AdminProducts /></RequireAdminAuth>} />
          <Route path="/admin/products/:id" element={<RequireAdminAuth><AdminProductDetails /></RequireAdminAuth>} />
          <Route path="/admin/products/upload" element={<RequireAdminAuth><UploadProducts /></RequireAdminAuth>} />
          <Route path="/admin/orders" element={<RequireAdminAuth><AdminOrders /></RequireAdminAuth>} />
          <Route path="/admin/orders/:id" element={<RequireAdminAuth><AdminOrderDetails /></RequireAdminAuth>} />
          <Route path="/admin/profile" element={<RequireAdminAuth><AdminProfile /></RequireAdminAuth>} />
          <Route path="/admin/update-profile" element={<RequireAdminAuth><AdminUpdateProfile /></RequireAdminAuth>} />
          
          <Route path="*" element={<Error404/>} />
        </Routes>
      </BrowserRouter>
  );
}

export default App;
```

#### Navbar.jsx
```javascript
import React, { useState, useContext } from 'react';
import { NavLink, Link } from 'react-router-dom';

// context
import { LoginContext } from '../context/LoginContextProvider';
import { CartContext } from '../context/CartContextProvider';

// icons
import { BiUser } from 'react-icons/bi';
import { AiOutlineShoppingCart } from 'react-icons/ai';
import { MdOutlineDashboardCustomize } from 'react-icons/md';

const Navbar = () => {
    const { currentAdmin } = useContext(LoginContext);
    const { cartItems } = useContext(CartContext);

    const [activeToggle, setActiveToggle] = useState(false);

    return (
        <div className="header">
            <nav className="navbar">
                <div className="logo">
                    <Link to="/" className="brandName link">eBazar</Link>
                </div>

                <div className={`navLinks ${activeToggle && 'activeNavLinks'}`}>
                    <NavLink to="/" className="link navLink">Home</NavLink>
                    <NavLink to="/products" className="link navLink">Products</NavLink>
                    <NavLink to="/about" className="link navLink">About Us</NavLink>
                    <NavLink to="/contact" className="link navLink">Contact</NavLink>
                </div>
                
                <div className="navIconLinks">
                    <Link to="/cart" className="link iconLink">
                        <AiOutlineShoppingCart className="navIcon"/>
                        <div className="cartCounter" style={(cartItems.length > 0) ? {display: 'flex'} : {display: 'none'}}>
                            <span>{cartItems.length}</span>
                        </div>
                    </Link>
                    {currentAdmin && <Link to="/admin/dashboard" className="link iconLink"><MdOutlineDashboardCustomize className="navIcon"/></Link>}
                    {!currentAdmin && <Link to="/user/profile" className="link iconLink"><BiUser className="navIcon"/></Link>}
                    <div className={`toggleBar iconLink ${activeToggle && 'activeToggler'}`} onClick={() => setActiveToggle(!activeToggle)}><span className="toggler"></span></div>
                </div>
            </nav>
        </div>
    );
}

export default Navbar;
```

#### Passing state with Link
```javascript
import React from 'react';
import { Link } from 'react-router-dom';

const Project = ({ project, projects }) => {
    const {id, title, desc, status, date, tags, thumbUrl, githubLink, previewLink} = project;

    return (
        <div className="projectContent">
            <div className="projectImage">
                <img src={thumbUrl} alt="project" />
            </div>

            <div className="projectOverview">
                <p className="projectTitle">
                    <Link to={id} state={{project, projects}} className="projectTitleLink">{title}</Link>
                </p>
                <p className="projectDesc">{(desc.length <= 60) ? desc : desc.slice(0, 60).concat('...')}</p>
                <p className="projectDate">{date}</p>
                <p className="tags">
                    {tags.map((elem, index) => {
                        return <span className={`${elem.toLowerCase()}`} style={elem === 'express' ? {color: '#222'} : null} key={index}>{elem}</span>
                    })}
                </p>
            </div>
        </div>
    );
}

export default Project;
```

#### Passing state with useNavigate
```javascript
import React, { useState, useEffect } from 'react';
import { Link, useNavigate } from 'react-router-dom';

// components
import AppLayout from '../layouts/AppLayout';
import Title from '../components/Title';
import CategoryLinks from '../components/home/CategoryLinks';
import SubProducts from '../components/products/SubProducts';

// icons
import { GoSearch } from 'react-icons/go';
import { FiPhoneCall } from 'react-icons/fi';
import { ProductsContext } from '../context/ProductsContextProvider';

// image
import bannarImg from '../assets/images/project/bannar.jpg';
import CallToAction from '../components/home/CallToAction';

const Home = () => {
    const [searchText, setSearchText] = useState('');

    const navigate = useNavigate();

    useEffect(() => {
        // search when 'Enter' key press
        document.onkeydown = (e) => {
            if(e.keyCode === 13 && searchText !== '') {
                navigate('/products', {state: searchText});
            }
        }
    }, [searchText, navigate]);

    return (
        <AppLayout>
            <div className="home">
                <div className="homeHeader">
                    <div className="contactInfo">
                        <FiPhoneCall className="contactIcon"/>
                        <p className="contactTitle">+88 0123456789 | +88 01298765431 | 24/7</p>
                    </div>
                    <div className="searchBox">
                        <input type="text" value={searchText} onChange={handleSearch} className="search" placeholder="Search..." />
                        <Link to="/products" state={searchText} className="homeSearchIcon link">
                            <GoSearch className="searchIcon" />
                        </Link>
                    </div>
                </div>
            </div>
        </AppLayout>
    );
}

export default Home;
```


#### Get state value
```javascript
// router
<Route path='/products/:title' element={<Product />}>

// send state from parent component
<Link to={title} state={id, title, body}>Lear more</Link>
```

```javascript
// get state from child component
import { useParams, useLocation } from 'react-router-dom';

const Product = () => {
    const location = useLocation();
    const {id, title, body} = location.state;
}
```

#### Route parameter
```javascript
// router
<Route path='/products/:title' element={<Product />}>
```

```javascript
import { useParams } from 'react-router-dom';

const Product = () => {
    // get route parameter
    const { title } = useParams();
}
```

#### Query parameter
```javascript
// query: /user?id=101&age=24

// router
<Route path='/user' element={<User />}>
```

```javascript
import { useSearchParams } from 'react-router-dom';

const User = () => {
    const [searchParams, setSearchParams] = useSearchParams();
    
    // get search params
    const id = searchParams.get('id');
    const age = searchParams.get('age');
    
    // set search params
    setSearchParams({ name: 'riazul', age: 25 };
}
```
