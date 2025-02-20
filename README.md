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

app.listen(3000, () => console.log('Server running on port 3000'));