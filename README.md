
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Water Tracker</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f8ff;
        }
        .container {
            width: 90%;
            max-width: 375px;
            text-align: center;
            padding: 20px;
            background: white;
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        .button {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            font-size: 18px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .settings-button {
            position: absolute;
            top: 20px;
            right: 20px;
            font-size: 24px;
            cursor: pointer;
        }
        .hidden {
            display: none;
        }
    </style>
</head>
<body>
    <div class="settings-button" onclick="toggleSettings()">⚙️</div>
    <div class="container">
        <h1>Water Tracker</h1>
        <p id="water-intake">Total: 0 ml</p>
        <p id="water-target">Target: 2000 ml</p>
        <p id="water-remaining">Remaining: 2000 ml</p>
        <button class="button" onclick="addWater(100)">+100 ml</button>
        <button class="button" onclick="addWater(250)">+250 ml</button>
        <button class="button" onclick="addWater(500)">+500 ml</button>
        <input type="number" id="custom-ml" placeholder="Enter ml" min="1">
        <button class="button" onclick="addCustomWater()">Add</button>
        <button class="button" onclick="resetWaterIntake()">Reset Intake</button>
    </div>
    <div class="container hidden" id="settings">
        <h2>Settings</h2>
        <label>Water Goal (ml):</label>
        <input type="number" id="water-goal" placeholder="Enter goal" value="2000">
        <label>Reminder Interval (minutes):</label>
        <input type="number" id="reminder-time" placeholder="Enter time">
        <button class="button" onclick="saveSettings()">Save</button>
    </div>
    <script>
        let totalWater = 0;
        let waterGoal = 2000;
        let reminderInterval = 60;

        function updateDisplay() {
            document.getElementById('water-intake').innerText = `Total: ${totalWater} ml`;
            document.getElementById('water-remaining').innerText = `Remaining: ${Math.max(waterGoal - totalWater, 0)} ml`;
        }

        function addWater(amount) {
            totalWater += amount;
            updateDisplay();
        }

        function addCustomWater() {
            let customAmount = parseInt(document.getElementById('custom-ml').value);
            if (customAmount > 0) {
                addWater(customAmount);
            }
        }

        function resetWaterIntake() {
            totalWater = 0;
            updateDisplay();
        }

        function toggleSettings() {
            document.getElementById('settings').classList.toggle('hidden');
        }

        function saveSettings() {
            let newGoal = parseInt(document.getElementById('water-goal').value);
            let newReminder = parseInt(document.getElementById('reminder-time').value);
            if (newGoal > 0) {
                waterGoal = newGoal;
                document.getElementById('water-target').innerText = `Target: ${waterGoal} ml`;
                updateDisplay();
            }
            if (newReminder > 0) {
                reminderInterval = newReminder;
                setReminder();
            }
            alert("Settings Saved!");
            toggleSettings();
        }

        function setReminder() {
            if ('Notification' in window && Notification.permission !== 'denied') {
                Notification.requestPermission().then(permission => {
                    if (permission === 'granted') {
                        setInterval(() => {
                            let now = new Date();
                            let hour = now.getHours();
                            if (hour >= 9 && hour <= 23) {
                                new Notification('Time to drink water Karan!', {
                                    body: `Don't forget to stay hydrated!`
                                });
                            }
                        }, reminderInterval * 60000);
                    }
                });
            }
        }

        function resetDailyIntake() {
            let now = new Date();
            let midnight = new Date();
            midnight.setHours(0, 0, 0, 0);
            let timeUntilMidnight = midnight.getTime() + 86400000 - now.getTime();
            setTimeout(() => {
                totalWater = 0;
                updateDisplay();
                resetDailyIntake();
            }, timeUntilMidnight);
        }

        resetDailyIntake();
    </script>
</body>
</html>
