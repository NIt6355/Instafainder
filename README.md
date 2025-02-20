<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Find Nearby Instagram Users</title>
    <script src="script.js"></script>
</head>
<body>
    <h1>Find Nearby Instagram Users</h1>
    <button onclick="getLocation()">Find Users Near Me</button>
    <div id="users"></div>
</body>
</html>function getLocation() {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(showPosition);
    } else {
        alert("Geolocation is not supported by this browser.");
    }
}

function showPosition(position) {
    let lat = position.coords.latitude;
    let lon = position.coords.longitude;
    
    fetch(`/find-users?lat=${lat}&lon=${lon}`)
        .then(response => response.json())
        .then(data => {
            document.getElementById("users").innerHTML = JSON.stringify(data);
        })
        .catch(error => console.error('Error:', error));
}const express = require('express');
const app = express();

app.get('/find-users', (req, res) => {
    const { lat, lon } = req.query;

    // Fake data (Actual implementation will require a database)
    const users = [
        { name: "User1", instagram: "@user1", lat: lat, lon: lon },
        { name: "User2", instagram: "@user2", lat: lat + 0.01, lon: lon + 0.01 }
    ];

    res.json(users);
});

app.listen(3000, () => console.log('Server running on port 3000'));const express = require('express');
const app = express();

app.get('/find-users', (req, res) => {
    const { lat, lon } = req.query;

    // Fake data (Actual implementation will require a database)
    const users = [
        { name: "User1", instagram: "@user1", lat: lat, lon: lon },
        { name: "User2", instagram: "@user2", lat: lat + 0.01, lon: lon + 0.01 }
    ];

    res.json(users);
});

app.listen(3000, () => console.log('Server running on port 3000')); pwd  # (Linux/Mac) - Yeh aapke current directory ka path batayega
cd   # (Windows) - Yeh aapko current directory ka path dikhayegacd instagram-nearbyls   # (Linux/Mac)
dir  # (Windows)node server.jsnpm install express corsServer running on http://localhost:3000taskkill /PID <PID_NUMBER> /Flsof -i :3000
kill -9 <PID_NUMBER>function getLocation() {
    if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(showPosition);
    } else {
        alert("Geolocation is not supported by this browser.");
    }
}

function showPosition(position) {
    let lat = position.coords.latitude;
    let lon = position.coords.longitude;
    
    fetch(`/find-users?lat=${lat}&lon=${lon}`)
        .then(response => response.json())
        .then(data => {
            document.getElementById("users").innerHTML = JSON.stringify(data);
        })
        .catch(error => console.error('Error:', error));
}const express = require('express');
const app = express();

app.get('/find-users', (req, res) => {
    const { lat, lon } = req.query;

    // Fake data (Actual implementation will require a database)
    const users = [
        { name: "User1", instagram: "@user1", lat: lat, lon: lon },
        { name: "User2", instagram: "@user2", lat: lat + 0.01, lon: lon + 0.01 }
    ];

    res.json(users);
});

app.listen(3000, () => console.log('Server running on port 3000'));<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Find Nearby Instagram Users</title>
    <script src="script.js"></script>
</head>
<body>
    <h1>Find Nearby Instagram Users</h1>
    <button onclick="getLocation()">Find Users Near Me</button>
    <div id="users"></div>
</body>
</html>  {users.length === 0 ? <p>No users found</p> : users.map(user => (
    <p key={user._id}>{user.username} - {user.email}</p>
  ))}
</div>  {users.length === 0 ? <p>No users found</p> : users.map(user => (
    <p key={user._id}>{user.username} - {user.email}</p>
  ))}
</div>cd backend
node server.js

cd ../frontend
npm startimport React, { useState } from "react";
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

export default NearbyUsers;import React, { useState } from "react";
import axios from "axios";

function Register() {
  const [user, setUser] = useState({ username: "", email: "", password: "", lat: "", lng: "" });

  const handleChange = (e) => setUser({ ...user, [e.target.name]: e.target.value });

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      if (!user.username || !user.email || !user.password || !user.lat || !user.lng) {
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

export default Register;import React from "react";
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

export default App;cd ../frontend
npx create-react-app .
npm install axios react-router-dom