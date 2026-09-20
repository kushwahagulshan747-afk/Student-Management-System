

```
full-stack-workbook/
│
├── Week-01-React-Setup/
├── Week-02-Components-Props/
├── Week-03-useState/
├── Week-04-useEffect/
├── Week-05-React-Router/
├── Week-06-Advanced-Hooks/
├── Week-07-Node-API/
├── Week-08-Express-Routing/
├── Week-09-MongoDB-Mongoose/
├── Week-10-REST-API/
└── Week-11-Integration/
```

---

### **Week 01: React Setup & GitHub Initialization**

Just create a normal React app:

```bash
npx create-react-app week-01-react-setup
```

No extra code needed. Just push the default React app.

---

### **Week 02: Components & Props**

**src/App.js**
```jsx
import Header from './components/Header';
import StudentCard from './components/StudentCard';

function App() {
  return (
    <div>
      <Header title="Student Information" />
      <StudentCard name="Rahul Sharma" age={21} course="BCA" />
      <StudentCard name="Priya Patel" age={20} course="BCA" />
    </div>
  );
}

export default App;
```

**src/components/Header.js**
```jsx
function Header(props) {
  return <h1>{props.title}</h1>;
}
export default Header;
```

**src/components/StudentCard.js**
```jsx
function StudentCard(props) {
  return (
    <div style={{border: "1px solid #ccc", padding: "10px", margin: "10px"}}>
      <h3>Name: {props.name}</h3>
      <p>Age: {props.age}</p>
      <p>Course: {props.course}</p>
    </div>
  );
}
export default StudentCard;
```

---

### **Week 03: useState**

**src/App.js**
```jsx
import { useState } from 'react';

function App() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("");

  return (
    <div style={{padding: "20px"}}>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>

      <br /><br />
      <input 
        type="text" 
        value={name} 
        onChange={(e) => setName(e.target.value)} 
        placeholder="Enter your name" 
      />
      <h3>Hello, {name}</h3>
    </div>
  );
}

export default App;
```

---

### **Week 04: useEffect**

**src/App.js**
```jsx
import { useState, useEffect } from 'react';

function App() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    console.log("Component Mounted");

    const interval = setInterval(() => {
      setSeconds(prev => prev + 1);
    }, 1000);

    return () => {
      clearInterval(interval);
      console.log("Component Unmounted");
    };
  }, []);

  return (
    <div style={{padding: "20px"}}>
      <h2>Timer: {seconds} seconds</h2>
    </div>
  );
}

export default App;
```

---

### **Week 05: React Router**

First install:
```bash
npm install react-router-dom
```

**src/App.js**
```jsx
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';
import Home from './pages/Home';
import About from './pages/About';
import Contact from './pages/Contact';

function App() {
  return (
    <BrowserRouter>
      <nav style={{padding: "10px", background: "#eee"}}>
        <Link to="/" style={{margin: "0 10px"}}>Home</Link>
        <Link to="/about" style={{margin: "0 10px"}}>About</Link>
        <Link to="/contact" style={{margin: "0 10px"}}>Contact</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

**src/pages/Home.js**
```jsx
function Home() {
  return <h2>Home Page</h2>;
}
export default Home;
```

**src/pages/About.js**
```jsx
function About() {
  return <h2>About Page</h2>;
}
export default About;
```

**src/pages/Contact.js**
```jsx
function Contact() {
  return <h2>Contact Page</h2>;
}
export default Contact;
```

---

### **Week 06: Advanced Hooks**

**src/App.js**
```jsx
import { useReducer, useRef, useMemo } from 'react';

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment': return { count: state.count + 1 };
    case 'decrement': return { count: state.count - 1 };
    default: return state;
  }
}

function App() {
  const [state, dispatch] = useReducer(reducer, initialState);
  const inputRef = useRef(null);
  const doubled = useMemo(() => state.count * 2, [state.count]);

  return (
    <div style={{padding: "20px"}}>
      <h2>Count: {state.count}</h2>
      <h3>Doubled: {doubled}</h3>
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>

      <br /><br />
      <input ref={inputRef} placeholder="Focus me" />
      <button onClick={() => inputRef.current.focus()}>Focus Input</button>
    </div>
  );
}

export default App;
```

---

### **Week 07: Node.js + Basic API**

**server.js**
```js
const express = require('express');
const app = express();

app.use(express.json());

app.get('/api/students', (req, res) => {
  res.json([
    { id: 1, name: "Rahul" },
    { id: 2, name: "Priya" }
  ]);
});

app.post('/api/students', (req, res) => {
  res.json({ message: "Student added", data: req.body });
});

app.listen(3000, () => {
  console.log("Server running on http://localhost:3000");
});
```

**package.json** (important)
```json
{
  "name": "week-07-api",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

---

### **Week 08: Express Routing**

**server.js**
```js
const express = require('express');
const app = express();
const studentRoutes = require('./routes/students');

app.use(express.json());
app.use('/api/students', studentRoutes);

app.listen(3000, () => console.log("Server running on port 3000"));
```

**routes/students.js**
```js
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  res.json([{ id: 1, name: "Rahul" }, { id: 2, name: "Priya" }]);
});

router.get('/:id', (req, res) => {
  res.json({ id: req.params.id, name: "Student " + req.params.id });
});

router.post('/', (req, res) => {
  res.json({ message: "Student created", data: req.body });
});

module.exports = router;
```

---

### **Week 09: MongoDB + Mongoose**

**db.js**
```js
const mongoose = require('mongoose');

mongoose.connect('mongodb://127.0.0.1:27017/studentDB')
  .then(() => console.log("MongoDB Connected"))
  .catch(err => console.log(err));
```

**models/Student.js**
```js
const mongoose = require('mongoose');

const studentSchema = new mongoose.Schema({
  name: String,
  age: Number,
  course: String,
  email: String
});

module.exports = mongoose.model('Student', studentSchema);
```

---

### **Week 10: Complete REST API**

**routes/students.js**
```js
const express = require('express');
const router = express.Router();
const Student = require('../models/Student');

// GET all
router.get('/', async (req, res) => {
  const students = await Student.find();
  res.json(students);
});

// GET one
router.get('/:id', async (req, res) => {
  const student = await Student.findById(req.params.id);
  res.json(student);
});

// CREATE
router.post('/', async (req, res) => {
  const student = new Student(req.body);
  await student.save();
  res.status(201).json(student);
});

// UPDATE
router.put('/:id', async (req, res) => {
  const student = await Student.findByIdAndUpdate(req.params.id, req.body, { new: true });
  res.json(student);
});

// DELETE
router.delete('/:id', async (req, res) => {
  await Student.findByIdAndDelete(req.params.id);
  res.json({ message: "Deleted successfully" });
});

module.exports = router;
```

---

### **Week 11: Frontend + Backend Integration**

**React side (App.js)**
```jsx
import { useState, useEffect } from 'react';
import axios from 'axios';

function App() {
  const [students, setStudents] = useState([]);
  const [name, setName] = useState("");

  useEffect(() => {
    axios.get('http://localhost:3000/api/students')
      .then(res => setStudents(res.data));
  }, []);

  const addStudent = async () => {
    const res = await axios.post('http://localhost:3000/api/students', { name });
    setStudents([...students, res.data]);
    setName("");
  };

  return (
    <div style={{padding: "20px"}}>
      <h2>Students</h2>
      <input value={name} onChange={e => setName(e.target.value)} placeholder="Enter name" />
      <button onClick={addStudent}>Add Student</button>

      <ul>
        {students.map(s => (
          <li key={s._id || s.name}>{s.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

**Backend – Enable CORS**
```js
const cors = require('cors');
app.use(cors());
```
