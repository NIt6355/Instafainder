
const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");

const app = express();
app.use(express.json());
app.use(cors());

// MongoDB Connection
const mongoURI = "mongodb://127.0.0.1:27017/yourdbname"; // Change `yourdbname`
mongoose.connect(mongoURI, {
  useNewUrlParser: true,
  useUnifiedTopology: true,
})
.then(() => console.log("MongoDB Connected"))
.catch((err) => console.error("MongoDB connection error:", err));

// User Schema
const UserSchema = new mongoose.Schema({
  username: String,
  email: String,
  password: String,
  location: {
    type: { type: String, enum: ["Point"], default: "Point" },
    coordinates: { type: [Number], required: true },
  },
});
UserSchema.index({ location: "2dsphere" });
const User = mongoose.model("User", UserSchema);

// Register User
app.post("/register", async (req, res) => {
  try {
    const { username, email, password, location } = req.body;
    const newUser = new User({ username, email, password, location });
    await newUser.save();
    res.json({ message: "User registered successfully" });
  } catch (error) {
    res.status(500).json({ error: "Registration failed" });
  }
});

// Find Nearby Users
app.get("/nearby", async (req, res) => {
  try {
    const { lat, lng } = req.query;
    const users = await User.find({
      location: {
        $near: {
          $geometry: { type: "Point", coordinates: [parseFloat(lng), parseFloat(lat)] },
          $maxDistance: 5000, // 5km range
        },
      },
    });
    res.json(users);
  } catch (error) {
    res.status(500).json({ error: "Error fetching users" });
  }
});

const PORT = 5000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));npm install axios react-router-domimport React from "react";
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import Register from "./components/Register";
import NearbyUsers from "./components/NearbyUsers";

function App() {
  return (
    <Router>
      <div>
        <h2>Welcome to Our Website</h2>
        <Routes>
          <Route path="/" element={<Register />} />
          <Route path="/nearby" element={<NearbyUsers />} />
        </Routes>
      </div>
    </Router>
  );
}

export default App;import React, { useState } from "react";
import axios from "axios";

function Register() {
  const [user, setUser] = useState({
    username: "",
    email: "",
    password: "",
    lat: "",
    lng: "",
  });

  const handleChange = (e) => {
    setUser({ ...user, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const location = { type: "Point", coordinates: [parseFloat(user.lng), parseFloat(user.lat)] };
      const response = await axios.post("http://localhost:5000/register", {
        username: user.username,
        email: user.email,
        password: user.password,
        location: location,
      });
      alert(response.data.message);
    } catch (error) {
      alert("❌ Registration failed: " + (error.response?.data?.error || error.message));
    }
  };

  return (
    <div>
      <h2>Register</h2>
      <form onSubmit={handleSubmit}>
        <input type="text" name="username" placeholder="Username" onChange={handleChange} required />
        <input type="email" name="email" placeholder="Email" onChange={handleChange} required />
        <input type="password" name="password" placeholder="Password" onChange={handleChange} required />
        <input type="text" name="lat" placeholder="Latitude" onChange={handleChange} required />
        <input type="text" name="lng" placeholder="Longitude" onChange={handleChange} required />
        <button type="submit">Register</button>
      </form>
    </div>
  );
}

export default Register;import React, { useState } from "react";
import axios from "axios";

function NearbyUsers() {
  const [location, setLocation] = useState({ lat: "", lng: "" });
  const [users, setUsers] = useState([]);

  const handleChange = (e) => {
    setLocation({ ...location, [e.target.name]: e.target.value });
  };

  const fetchNearbyUsers = async () => {
    try {
      const response = await axios.get(`http://localhost:5000/nearby?lat=${location.lat}&lng=${location.lng}`);
      setUsers(response.data);
    } catch (error) {
      alert("❌ Error fetching users: " + (error.response?.data?.error || error.message));
    }
  };

  return (
    <div>
      <h2>Find Nearby Users</h2>
      <input type="text" name="lat" placeholder="Latitude" onChange={handleChange} required />
      <input type="text" name="lng" placeholder="Longitude" onChange={handleChange} required />
      <button onClick={fetchNearbyUsers}>Search</button>
      <ul>
        {users.length === 0 ? (
          <p>No users found</p>
        ) : (
          users.map((user) => (
            <li key={user._id}>
              {user.username} - {user.email}
            </li>
          ))
        )}
      </ul>
    </div>
  );
}

export default NearbyUsers;cd backend
npm install  # Install dependenciescd backend
node server.jscd frontend
npm install  # Install dependencies
npm start    # Start React appmyproject/
│── backend/
│── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Register.js
│   │   │   ├── NearbyUsers.js
│   │   ├── App.js
│   │   ├── index.js
│   ├── package.jsonimport React from "react";
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import Register from "./components/Register";
import NearbyUsers from "./components/NearbyUsers";

function App() {
  return (
    <Router>
      <div>
        <h2>🌍 Welcome to Our Website</h2>
        <Routes>
          <Route path="/" element={<Register />} />
          <Route path="/nearby" element={<NearbyUsers />} />
        </Routes>
      </div>
    </Router>
  );
}

export default App;import React, { useState } from "react";
import axios from "axios";

function Register() {
  const [user, setUser] = useState({
    username: "",
    email: "",
    password: "",
    lat: "",
    lng: "",
  });

  const handleChange = (e) => {
    setUser({ ...user, [e.target.name]: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      const location = { type: "Point", coordinates: [parseFloat(user.lng), parseFloat(user.lat)] };
      const response = await axios.post("http://localhost:5000/register", {
        username: user.username,
        email: user.email,
        password: user.password,
        location: location,
      });
      alert(response.data.message);
    } catch (error) {
      alert("❌ Registration failed: " + (error.response?.data?.error || error.message));
    }
  };

  return (
    <div>
      <h2>Register</h2>
      <form onSubmit={handleSubmit}>
        <input type="text" name="username" placeholder="Username" onChange={handleChange} required />
        <input type="email" name="email" placeholder="Email" onChange={handleChange} required />
        <input type="password" name="password" placeholder="Password" onChange={handleChange} required />
        <input type="text" name="lat" placeholder="Latitude" onChange={handleChange} required />
        <input type="text" name="lng" placeholder="Longitude" onChange={handleChange} required />
        <button type="submit">Register</button>
      </form>
    </div>
  );
}

export default Register;import React, { useState } from "react";
import axios from "axios";

function NearbyUsers() {
  const [location, setLocation] = useState({ lat: "", lng: "" });
  const [users, setUsers] = useState([]);

  const handleChange = (e) => {
    setLocation({ ...location, [e.target.name]: e.target.value });
  };

  const fetchNearbyUsers = async () => {
    try {
      const response = await axios.get(`http://localhost:5000/nearby?lat=${location.lat}&lng=${location.lng}`);
      setUsers(response.data);
    } catch (error) {
      alert("❌ Error fetching users: " + (error.response?.data?.error || error.message));
    }
  };

  return (
    <div>
      <h2>Find Nearby Users</h2>
      <input type="text" name="lat" placeholder="Latitude" onChange={handleChange} required />
      <input type="text" name="lng" placeholder="Longitude" onChange={handleChange} required />
      <button onClick={fetchNearbyUsers}>Search</button>
      <ul>
        {users.length === 0 ? <li>No users found</li> : users.map((user) => (
          <li key={user._id}>{user.username} - {user.email}</li>
        ))}
      </ul>
    </div>
  );
}

export default NearbyUsers;http://localhost:3000
node server.js  # Start backend curl -X POST http://localhost:5000/register -H "Content-Type: application/json" -d '{
  "username": "testuser",
  "email": "test@email.com",
  "password": "123456",
  "location": {
    "type": "Point",
    "coordinates": [77.1025, 28.7041]
  }
}'const cors = require('cors');
app.use(cors());catch (error) {
    console.log("Registration Error:", error);
    alert("❌ Registration failed. Check console for details.");
}
