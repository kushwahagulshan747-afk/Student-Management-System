
student-management-system/
│
├── backend/
│   ├── server.js
│   ├── db.js
│   ├── models/
│   │   └── Student.js
│   ├── routes/
│   │   └── students.js
│   └── package.json
│
└── frontend/
    ├── package.json
    └── src/
        ├── App.js
        ├── index.js
        └── components/
            ├── StudentList.js
            ├── AddStudent.js
            └── EditStudent.js
```

---

### 1. Backend Code

#### `backend/package.json`
```json
{
  "name": "student-management-backend",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
  "dependencies": {
    "cors": "^2.8.5",
    "express": "^4.18.2",
    "mongoose": "^7.5.0"
  }
}
```

#### `backend/db.js`
```js
const mongoose = require('mongoose');

mongoose.connect('mongodb://127.0.0.1:27017/studentDB')
  .then(() => console.log("MongoDB Connected Successfully"))
  .catch((err) => console.log("MongoDB Connection Error:", err));
```

#### `backend/models/Student.js`
```js
const mongoose = require('mongoose');

const studentSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true
  },
  age: {
    type: Number,
    required: true
  },
  course: {
    type: String,
    required: true
  },
  email: {
    type: String,
    required: true
  }
}, { timestamps: true });

module.exports = mongoose.model('Student', studentSchema);
```

#### `backend/routes/students.js`
```js
const express = require('express');
const router = express.Router();
const Student = require('../models/Student');

// GET all students
router.get('/', async (req, res) => {
  try {
    const students = await Student.find().sort({ createdAt: -1 });
    res.json(students);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
});

// GET single student
router.get('/:id', async (req, res) => {
  try {
    const student = await Student.findById(req.params.id);
    if (!student) return res.status(404).json({ message: "Student not found" });
    res.json(student);
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
});

// CREATE student
router.post('/', async (req, res) => {
  try {
    const student = new Student(req.body);
    const savedStudent = await student.save();
    res.status(201).json(savedStudent);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
});

// UPDATE student
router.put('/:id', async (req, res) => {
  try {
    const student = await Student.findByIdAndUpdate(
      req.params.id,
      req.body,
      { new: true }
    );
    if (!student) return res.status(404).json({ message: "Student not found" });
    res.json(student);
  } catch (err) {
    res.status(400).json({ message: err.message });
  }
});

// DELETE student
router.delete('/:id', async (req, res) => {
  try {
    const student = await Student.findByIdAndDelete(req.params.id);
    if (!student) return res.status(404).json({ message: "Student not found" });
    res.json({ message: "Student deleted successfully" });
  } catch (err) {
    res.status(500).json({ message: err.message });
  }
});

module.exports = router;
```

#### `backend/server.js`
```js
const express = require('express');
const cors = require('cors');
require('./db');

const studentRoutes = require('./routes/students');

const app = express();

app.use(cors());
app.use(express.json());

app.use('/api/students', studentRoutes);

app.get('/', (req, res) => {
  res.send("Student Management System API is running...");
});

const PORT = 5000;
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

---

### 2. Frontend Code

#### `frontend/package.json`
```json
{
  "name": "student-management-frontend",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "axios": "^1.5.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-scripts": "5.0.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build"
  }
}
```

#### `frontend/src/App.js`
```jsx
import { useState, useEffect } from 'react';
import axios from 'axios';
import StudentList from './components/StudentList';
import AddStudent from './components/AddStudent';
import EditStudent from './components/EditStudent';

function App() {
  const [students, setStudents] = useState([]);
  const [editingStudent, setEditingStudent] = useState(null);
  const [showAddForm, setShowAddForm] = useState(false);

  // Fetch all students
  const fetchStudents = async () => {
    try {
      const res = await axios.get('http://localhost:5000/api/students');
      setStudents(res.data);
    } catch (err) {
      console.error("Error fetching students:", err);
    }
  };

  useEffect(() => {
    fetchStudents();
  }, []);

  // Add student
  const handleAdd = async (studentData) => {
    try {
      await axios.post('http://localhost:5000/api/students', studentData);
      fetchStudents();
      setShowAddForm(false);
    } catch (err) {
      alert("Error adding student");
    }
  };

  // Update student
  const handleUpdate = async (id, studentData) => {
    try {
      await axios.put(`http://localhost:5000/api/students/${id}`, studentData);
      fetchStudents();
      setEditingStudent(null);
    } catch (err) {
      alert("Error updating student");
    }
  };

  // Delete student
  const handleDelete = async (id) => {
    if (window.confirm("Are you sure you want to delete this student?")) {
      try {
        await axios.delete(`http://localhost:5000/api/students/${id}`);
        fetchStudents();
      } catch (err) {
        alert("Error deleting student");
      }
    }
  };

  return (
    <div style={{ maxWidth: "900px", margin: "0 auto", padding: "20px", fontFamily: "Arial" }}>
      <h1 style={{ textAlign: "center", color: "#333" }}>Student Management System</h1>

      <div style={{ textAlign: "center", marginBottom: "20px" }}>
        <button
          onClick={() => {
            setShowAddForm(!showAddForm);
            setEditingStudent(null);
          }}
          style={{
            padding: "10px 20px",
            backgroundColor: "#4CAF50",
            color: "white",
            border: "none",
            borderRadius: "5px",
            cursor: "pointer",
            fontSize: "16px"
          }}
        >
          {showAddForm ? "Close Form" : "Add New Student"}
        </button>
      </div>

      {showAddForm && (
        <AddStudent onAdd={handleAdd} onCancel={() => setShowAddForm(false)} />
      )}

      {editingStudent && (
        <EditStudent
          student={editingStudent}
          onUpdate={handleUpdate}
          onCancel={() => setEditingStudent(null)}
        />
      )}

      <StudentList
        students={students}
        onEdit={setEditingStudent}
        onDelete={handleDelete}
      />
    </div>
  );
}

export default App;
```

#### `frontend/src/components/StudentList.js`
```jsx
function StudentList({ students, onEdit, onDelete }) {
  return (
    <div>
      <h2>Students List ({students.length})</h2>

      {students.length === 0 ? (
        <p>No students found. Please add a student.</p>
      ) : (
        <table style={{ width: "100%", borderCollapse: "collapse", marginTop: "20px" }}>
          <thead>
            <tr style={{ backgroundColor: "#f2f2f2" }}>
              <th style={{ border: "1px solid #ddd", padding: "12px" }}>Name</th>
              <th style={{ border: "1px solid #ddd", padding: "12px" }}>Age</th>
              <th style={{ border: "1px solid #ddd", padding: "12px" }}>Course</th>
              <th style={{ border: "1px solid #ddd", padding: "12px" }}>Email</th>
              <th style={{ border: "1px solid #ddd", padding: "12px" }}>Actions</th>
            </tr>
          </thead>
          <tbody>
            {students.map((student) => (
              <tr key={student._id}>
                <td style={{ border: "1px solid #ddd", padding: "12px" }}>{student.name}</td>
                <td style={{ border: "1px solid #ddd", padding: "12px" }}>{student.age}</td>
                <td style={{ border: "1px solid #ddd", padding: "12px" }}>{student.course}</td>
                <td style={{ border: "1px solid #ddd", padding: "12px" }}>{student.email}</td>
                <td style={{ border: "1px solid #ddd", padding: "12px" }}>
                  <button
                    onClick={() => onEdit(student)}
                    style={{
                      marginRight: "8px",
                      padding: "6px 12px",
                      backgroundColor: "#2196F3",
                      color: "white",
                      border: "none",
                      borderRadius: "4px",
                      cursor: "pointer"
                    }}
                  >
                    Edit
                  </button>
                  <button
                    onClick={() => onDelete(student._id)}
                    style={{
                      padding: "6px 12px",
                      backgroundColor: "#f44336",
                      color: "white",
                      border: "none",
                      borderRadius: "4px",
                      cursor: "pointer"
                    }}
                  >
                    Delete
                  </button>
                </td>
              </tr>
            ))}
          </tbody>
        </table>
      )}
    </div>
  );
}

export default StudentList;
```

#### `frontend/src/components/AddStudent.js`
```jsx
import { useState } from 'react';

function AddStudent({ onAdd, onCancel }) {
  const [formData, setFormData] = useState({
    name: '',
    age: '',
    course: '',
    email: ''
  });

  const handleChange = (e) => {
    setFormData({
      ...formData,
      [e.target.name]: e.target.value
    });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!formData.name || !formData.age || !formData.course || !formData.email) {
      alert("Please fill all fields");
      return;
    }
    onAdd({
      ...formData,
      age: Number(formData.age)
    });
    setFormData({ name: '', age: '', course: '', email: '' });
  };

  return (
    <div style={{
      border: "1px solid #ddd",
      padding: "20px",
      marginBottom: "20px",
      borderRadius: "8px",
      backgroundColor: "#f9f9f9"
    }}>
      <h3>Add New Student</h3>
      <form onSubmit={handleSubmit}>
        <div style={{ marginBottom: "12px" }}>
          <input
            type="text"
            name="name"
            placeholder="Name"
            value={formData.name}
            onChange={handleChange}
            style={{ width: "100%", padding: "8px", fontSize: "16px" }}
          />
        </div>
        <div style={{ marginBottom: "12px" }}>
          <input
            type="number"
            name="age"
            placeholder="Age"
            value={formData.age}
            onChange={handleChange}
            style={{ width: "100%", padding: "8px", fontSize: "16px" }}
          />
        </div>
        <div style={{ marginBottom: "12px" }}>
          <input
            type="text"
            name="course"
            placeholder="Course"
            value={formData.course}
            onChange={handleChange}
            style={{ width: "100%", padding: "8px", fontSize: "16px" }}
          />
        </div>
        <div style={{ marginBottom: "12px" }}>
          <input
            type="email"
            name="email"
            placeholder="Email"
            value={formData.email}
            onChange={handleChange}
            style={{ width: "100%", padding: "8px", fontSize: "16px" }}
          />
        </div>
        <button
          type="submit"
          style={{
            padding: "10px 20px",
            backgroundColor: "#4CAF50",
            color: "white",
            border: "none",
            borderRadius: "5px",
            cursor: "pointer",
            marginRight: "10px"
          }}
        >
          Add Student
        </button>
        <button
          type="button"
          onClick={onCancel}
          style={{
            padding: "10px 20px",
            backgroundColor: "#9e9e9e",
            color: "white",
            border: "none",
            borderRadius: "5px",
            cursor: "pointer"
          }}
        >
          Cancel
        </button>
      </form>
    </div>
  );
}

export default AddStudent;
```

#### `frontend/src/components/EditStudent.js`
```jsx
import { useState, useEffect } from 'react';

function EditStudent({ student, onUpdate, onCancel }) {
  const [formData, setFormData] = useState({
    name: '',
    age: '',
    course: '',
    email: ''
  });

  useEffect(() => {
    if (student) {
      setFormData({
        name: student.name,
        age: student.age,
        course: student.course,
        email: student.email
      });
    }
  }, [student]);

  const handleChange = (e) => {
    setFormData({
      ...formData,
      [e.target.name]: e.target.value
    });
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    onUpdate(student._id, {
      ...formData,
      age: Number(formData.age)
    });
  };

  return (
    <div style={{
      border: "1px solid #2196F3",
      padding: "20px",
      marginBottom: "20px",
      borderRadius: "8px",
      backgroundColor: "#e3f2fd"
    }}>
      <h3>Edit Student</h3>
      <form onSubmit={handleSubmit}>
        <div style={{ marginBottom: "12px" }}>
          <input
            type="text"
            name="name"
            placeholder="Name"
            value={formData.name}
            onChange={handleChange}
            style={{ width: "100%", padding: "8px", fontSize: "16px" }}
          />
        </div>
        <div style={{ marginBottom: "12px" }}>
          <input
            type="number"
            name="age"
            placeholder="Age"
            value={formData.age}
            onChange={handleChange}
            style={{ width: "100%", padding: "8px", fontSize: "16px" }}
          />
        </div>
        <div style={{ marginBottom: "12px" }}>
          <input
            type="text"
            name="course"
            placeholder="Course"
            value={formData.course}
            onChange={handleChange}
            style={{ width: "100%", padding: "8px", fontSize: "16px" }}
          />
        </div>
        <div style={{ marginBottom: "12px" }}>
          <input
            type="email"
            name="email"
            placeholder="Email"
            value={formData.email}
            onChange={handleChange}
            style={{ width: "100%", padding: "8px", fontSize: "16px" }}
          />
        </div>
        <button
          type="submit"
          style={{
            padding: "10px 20px",
            backgroundColor: "#2196F3",
            color: "white",
            border: "none",
            borderRadius: "5px",
            cursor: "pointer",
            marginRight: "10px"
          }}
        >
          Update Student
        </button>
        <button
          type="button"
          onClick={onCancel}
          style={{
            padding: "10px 20px",
            backgroundColor: "#9e9e9e",
            color: "white",
            border: "none",
            borderRadius: "5px",
            cursor: "pointer"
          }}
        >
          Cancel
        </button>
      </form>
    </div>
  );
}

export default EditStudent;
