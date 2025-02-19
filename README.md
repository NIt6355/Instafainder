npx create-react-app frontend
cd frontend
npm install axios react-router-dommyproject/
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
      alert("Registration failed: " + error.response.data.error);
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
      alert("Error fetching users: " + error.response.data.error);
    }
  };

  return (
    <div>
      <h2>Find Nearby Users</h2>
      <input type="text" name="lat" placeholder="Latitude" onChange={handleChange} required />
      <input type="text" name="lng" placeholder="Longitude" onChange={handleChange} required />
      <button onClick={fetchNearbyUsers}>Search</button>
      <ul>
        {users.map((user) => (
          <li key={user._id}>{user.username} - {user.email}</li>
        ))}
      </ul>
    </div>
  );
}

export default NearbyUsers;npm start
