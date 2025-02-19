instafainder/
│── backend/
│   ├── server.js
│   ├── models/
│   │   ├── User.js
│   ├── package.json
│   ├── .env
│── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Register.js
│   │   │   ├── NearbyUsers.js
│   │   ├── App.js
│   │   ├── index.js
│   ├── package.jsonPORT=5000
MONGO_URI=mongodb://127.0.0.1:27017/instafaindercd backend
npm init -y
npm install express mongoose cors dotenvrequire("dotenv").config();
const express = require("express");
const mongoose = require("mongoose");
const cors = require("cors");

const app = express();
app.use(express.json());
app.use(cors());

// MongoDB Connection
mongoose.connect(process.env.MONGO_URI, {
    useNewUrlParser: true,
    useUnifiedTopology: true,
})
.then(() => console.log("✅ MongoDB Connected"))
.catch((err) => console.error("❌ MongoDB connection error:", err));

// User Schema
const UserSchema = new mongoose.Schema({
    username: String,
    email: String,
    password: String,
    location: {
        type: { type: String, enum: ["Point"], default: "Point" },
        coordinates: { type: [Number], required: true }
    }
});
UserSchema.index({ location: "2dsphere" });
const User = mongoose.model("User", UserSchema);

// Register User API
app.post("/register", async (req, res) => {
    try {
        const { username, email, password, location } = req.body;
        if (!username || !email || !password || !location || !location.coordinates) {
            return res.status(400).json({ error: "All fields are required" });
        }

        const newUser = new User({ username, email, password, location });
        await newUser.save();
        res.json({ message: "✅ User registered successfully" });
    } catch (error) {
        console.error("❌ Registration Error:", error);
        res.status(500).json({ error: "Registration failed. Check server logs." });
    }
});

// Find Nearby Users API
app.get("/nearby", async (req, res) => {
    try {
        const { lat, lng } = req.query;
        if (!lat || !lng) {
            return res.status(400).json({ error: "Latitude and Longitude required" });
        }

        const users = await User.find({
            location: {
                $near: {
                    $geometry: { type: "Point", coordinates: [parseFloat(lng), parseFloat(lat)] },
                    $maxDistance: 5000 // 5km range
                }
            }
        });

        res.json(users);
    } catch (error) {
        console.error("❌ Error fetching users:", error);
        res.status(500).json({ error: "Error fetching users" });
    }
});

// Start Server
const PORT = process.env.PORT || 5000;
app.listen(PORT, () => console.log(`🚀 Server running on port ${PORT}`));cd ../frontend
npx create-react-app .
npm install axios react-router-domimport React from "react";
import { BrowserRouter as Router, Route, Routes } from "react-router-dom";
import Register from "./components/Register";
import NearbyUsers from "./components/NearbyUsers";

function App() {
  return (
    <Router>
      <div>
        <h1>🌍 Welcome to Instafainder</h1>
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
  const [user, setUser] = useState({ username: "", email: "", password: "", lat: "", lng: "" });

  const handleChange = (e) => setUser({ ...user, [e.target.name]: e.target.value });

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      nitesh_s_olanki|| Solankinitesh836@gmail.com || solankinitesh6355|| !user.lat || !user.lng) {
        alert("⚠️ Please fill all fields");
        return;
      }

      const location = { type: "Point", coordinates: [parseFloat(user.lng), parseFloat(user.lat)] };
      const response = await axios.post("http://localhost:5000/register", { ...user, location });

      alert(response.data.message);
    } catch (error) {
      console.error("❌ Registration Error:", error);
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

  const handleChange = (e) => setLocation({ ...location, [e.target.name]: e.target.value });

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

      {users.length === 0 ? <p>No users found</p> : users.map(user => (
        <p key={user._id}>{user.username} - {user.email}</p>
      ))}
    </div>
  );
}

export default NearbyUsers;cd backend
node server.js

cd ../frontend
npm start
