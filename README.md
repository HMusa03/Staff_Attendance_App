<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Advanced Staff Attendance App</title>
    <style>
        :root {
            --primary-color: #e0e5ec;
            --text-color: #333;
            --shadow-color: #a3b1c6;
            --highlight-color: #ffffff;
            --accent-color: #4a90e2;
        }

        body {
            font-family: 'Roboto', Arial, sans-serif;
            background-color: var(--primary-color);
            color: var(--text-color);
            margin: 0;
            padding: 20px;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            transition: all 0.3s ease;
        }

        .container {
            background-color: var(--primary-color);
            border-radius: 20px;
            padding: 30px;
            box-shadow: 
                8px 8px 15px var(--shadow-color),
                -8px -8px 15px var(--highlight-color);
            max-width: 400px;
            width: 100%;
        }

        h1, h2 {
            text-align: center;
            margin-bottom: 30px;
            color: var(--accent-color);
        }

        .form-group {
            margin-bottom: 20px;
        }

        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }

        input[type="text"],
        input[type="password"] {
            width: 100%;
            padding: 12px;
            border: none;
            background-color: var(--primary-color);
            border-radius: 10px;
            box-shadow: 
                inset 5px 5px 10px var(--shadow-color),
                inset -5px -5px 10px var(--highlight-color);
            transition: all 0.3s ease;
        }

        input[type="text"]:focus,
        input[type="password"]:focus {
            outline: none;
            box-shadow: 
                inset 3px 3px 5px var(--shadow-color),
                inset -3px -3px 5px var(--highlight-color);
        }

        button {
            width: 100%;
            padding: 12px;
            background-color: var(--accent-color);
            border: none;
            border-radius: 10px;
            color: white;
            font-weight: bold;
            cursor: pointer;
            transition: all 0.3s ease;
            box-shadow: 
                5px 5px 10px var(--shadow-color),
                -5px -5px 10px var(--highlight-color);
        }

        button:hover {
            background-color: #3a80d2;
        }

        button:active {
            box-shadow: 
                inset 2px 2px 5px var(--shadow-color),
                inset -2px -2px 5px var(--highlight-color);
        }

        #attendanceHistory {
            margin-top: 30px;
        }

        #attendanceList {
            list-style-type: none;
            padding: 0;
        }

        #attendanceList li {
            background-color: var(--primary-color);
            margin-bottom: 10px;
            padding: 15px;
            border-radius: 10px;
            box-shadow: 
                3px 3px 6px var(--shadow-color),
                -3px -3px 6px var(--highlight-color);
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        .hidden {
            display: none;
        }

        #locationInfo {
            text-align: center;
            margin-top: 20px;
            font-style: italic;
        }

        #darkModeToggle {
            position: absolute;
            top: 20px;
            right: 20px;
            background-color: var(--accent-color);
            color: white;
            border: none;
            border-radius: 50%;
            width: 50px;
            height: 50px;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .dark-mode {
            --primary-color: #2c3e50;
            --text-color: #ecf0f1;
            --shadow-color: #1a2733;
            --highlight-color: #3e5871;
            --accent-color: #3498db;
        }
    </style>
</head>
<body>
    <button id="darkModeToggle">🌓</button>
    <div class="container">
        <h1>Staff Attendance</h1>
        <div id="loginForm">
            <div class="form-group">
                <label for="username">Username:</label>
                <input type="text" id="username" required>
            </div>
            <div class="form-group">
                <label for="password">Password:</label>
                <input type="password" id="password" required>
            </div>
            <button onclick="login()">Login</button>
        </div>
        <div id="attendanceActions" class="hidden">
            <button id="clockInBtn" onclick="clockIn()">Clock In</button>
            <button id="clockOutBtn" onclick="clockOut()" style="display: none;">Clock Out</button>
        </div>
        <div id="locationInfo" class="hidden"></div>
        <div id="attendanceHistory" class="hidden">
            <h2>Attendance History</h2>
            <ul id="attendanceList"></ul>
        </div>
    </div>

    <script>
        let isLoggedIn = false;
        let isClockedIn = false;
        let attendanceData = [];

        function login() {
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;

            // In a real app, you'd validate credentials against a server
            if (username && password) {
                isLoggedIn = true;
                document.getElementById('loginForm').style.display = 'none';
                document.getElementById('attendanceActions').classList.remove('hidden');
                document.getElementById('attendanceHistory').classList.remove('hidden');
                document.getElementById('locationInfo').classList.remove('hidden');
                loadAttendanceHistory();
                updateLocationInfo();
            } else {
                alert('Please enter valid credentials');
            }
        }

        function clockIn() {
            if (!isClockedIn) {
                isClockedIn = true;
                const now = new Date();
                attendanceData.push({ type: 'Clock In', time: now.toLocaleString(), location: getCurrentLocation() });
                updateAttendanceHistory();
                document.getElementById('clockInBtn').style.display = 'none';
                document.getElementById('clockOutBtn').style.display = 'block';
            }
        }

        function clockOut() {
            if (isClockedIn) {
                isClockedIn = false;
                const now = new Date();
                attendanceData.push({ type: 'Clock Out', time: now.toLocaleString(), location: getCurrentLocation() });
                updateAttendanceHistory();
                document.getElementById('clockInBtn').style.display = 'block';
                document.getElementById('clockOutBtn').style.display = 'none';
            }
        }

        function loadAttendanceHistory() {
            // In a real app, you'd fetch this data from a server
            attendanceData = [
                { type: 'Clock In', time: '2023-05-01 09:00:00', location: 'Office' },
                { type: 'Clock Out', time: '2023-05-01 17:00:00', location: 'Office' },
                { type: 'Clock In', time: '2023-05-02 08:55:00', location: 'Home' },
                { type: 'Clock Out', time: '2023-05-02 17:05:00', location: 'Home' }
            ];
            updateAttendanceHistory();
        }

        function updateAttendanceHistory() {
            const list = document.getElementById('attendanceList');
            list.innerHTML = '';
            attendanceData.forEach(entry => {
                const li = document.createElement('li');
                li.innerHTML = `
                    <span>${entry.type}</span>
                    <span>${entry.time}</span>
                    <span>${entry.location}</span>
                `;
                list.appendChild(li);
            });
        }

        function getCurrentLocation() {
            // In a real app, you'd use geolocation API or get location from App Inventor
            return 'Current Location';
        }

        function updateLocationInfo() {
            const locationInfo = document.getElementById('locationInfo');
            locationInfo.textContent = `Current location: ${getCurrentLocation()}`;
        }

        // Dark mode toggle
        const darkModeToggle = document.getElementById('darkModeToggle');
        darkModeToggle.addEventListener('click', () => {
            document.body.classList.toggle('dark-mode');
        });

        // Initialize app state
        document.addEventListener('DOMContentLoaded', () => {
            if (!isLoggedIn) {
                document.getElementById('attendanceActions').classList.add('hidden');
                document.getElementById('attendanceHistory').classList.add('hidden');
                document.getElementById('locationInfo').classList.add('hidden');
            }
        });

        // Simulating communication with App Inventor
        function sendToAppInventor(data) {
            // In a real app, you'd use a method to communicate with App Inventor
            console.log('Sending to App Inventor:', data);
        }

        // Receive data from App Inventor
        function receiveFromAppInventor(data) {
            // Handle data received from App Inventor
            console.log('Received from App Inventor:', data);
        }
    </script>
</body>
</html>
