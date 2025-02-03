<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Emerson's Bolus Calculator</title>
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Bolus Calculator">
    <link rel="apple-touch-icon" href="icon.png">
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f8f9fa;
        }
        .container {
            width: 90%;
            max-width: 400px;
            margin: auto;
            background: white;
            padding: 15px;
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        h1 {
            text-align: center;
            margin-bottom: 15px;
            font-size: 20px;
        }
        .form-group {
            margin-bottom: 10px;
        }
        label {
            display: block;
            margin-bottom: 3px;
            font-size: 14px;
        }
        input, select, button {
            width: 100%;
            padding: 10px;
            font-size: 14px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        button {
            background-color: #007bff;
            color: white;
            cursor: pointer;
            margin-top: 10px;
        }
        button:hover {
            background-color: #0056b3;
        }
        .result {
            margin-top: 15px;
            font-size: 16px;
            font-weight: bold;
            text-align: center;
        }
        .calculations {
            display: none;
            margin-top: 10px;
            font-size: 12px;
            color: #555;
            background: #f1f1f1;
            padding: 8px;
            border-radius: 5px;
            box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.1);
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Emerson's Bolus Calculator</h1>
        <div class="form-group">
            <label for="currentBloodGlucose">Current Blood Glucose (mg/dL)</label>
            <input type="number" inputmode="numeric" pattern="[0-9]*" id="currentBloodGlucose" min="0" max="800" required>
        </div>
        <div class="form-group">
            <label for="carbs">Carbs in Meal (grams)</label>
            <input type="number" inputmode="numeric" pattern="[0-9]*" id="carbs" min="0" required>
        </div>
        <div class="form-group">
            <label for="targetBloodGlucose">Target Blood Glucose (mg/dL)</label>
            <select id="targetBloodGlucose">
                <script>
                    for (let i = 90; i <= 230; i += 10) {
                        let note = '';
                        if (i === 120) note = ' (normal meals)';
                        if (i === 170) note = ' (pre-swimming)';
                        document.write(`<option value="${i}" ${i === 120 ? 'selected' : ''}>${i} mg/dL${note}</option>`);
                    }
                </script>
            </select>
        </div>
        <div class="form-group">
            <label for="cf">Correction Factor (CF)</label>
            <select id="cf">
                <script>
                    for (let i = 0; i <= 200; i += 10) {
                        document.write(`<option value="${i}" ${i === 100 ? 'selected' : ''}>${i}</option>`);
                    }
                </script>
            </select>
        </div>
        <div class="form-group">
            <label for="icr">Insulin-to-Carb Ratio (ICR)</label>
            <select id="icr">
                <script>
                    for (let i = 5; i <= 60; i += 1) {
                        document.write(`<option value="${i}" ${i === 22 ? 'selected' : ''}>${i}</option>`);
                    }
                </script>
            </select>
        </div>
        <button onclick="calculateBolus()">Calculate Bolus</button>
        <div class="result" id="result"></div>
        <a href="#" onclick="toggleCalculations()" style="display:block; text-align:center; margin-top:10px; font-size:12px;">Show me the Math</a>
        <div class="calculations" id="calculations"></div>
    </div>

    <script>
        function calculateBolus() {
            const currentBloodGlucose = parseFloat(document.getElementById('currentBloodGlucose').value);
            const carbs = parseFloat(document.getElementById('carbs').value);
            const targetBloodGlucose = parseFloat(document.getElementById('targetBloodGlucose').value);
            const cf = parseFloat(document.getElementById('cf').value);
            const icr = parseFloat(document.getElementById('icr').value);

            if (isNaN(currentBloodGlucose) || isNaN(carbs)) {
                document.getElementById('result').innerText = 'Please enter valid values for Current Blood Glucose and Carbs.';
                return;
            }

            const carbBolus = carbs / icr;
            const correctionBolus = cf > 0 ? (currentBloodGlucose - targetBloodGlucose) / cf : 0;
            const totalBolus = carbBolus + correctionBolus;
            const roundedBolus = Math.round(totalBolus * 2) / 2;

            if (roundedBolus <= 0) {
                document.getElementById('result').innerText = 'No insulin bolus is needed.';
            } else {
                document.getElementById('result').innerText = `Total Bolus: ${totalBolus.toFixed(2)} units\nRounded Total Bolus: ${roundedBolus.toFixed(1)} units`;
            }
        }
    </script>

    <script>
        if ('serviceWorker' in navigator) {
            window.addEventListener('load', () => {
                navigator.serviceWorker.register('/service-worker.js')
                .then(registration => {
                    console.log('Service Worker registered:', registration);
                })
                .catch(error => {
                    console.log('Service Worker registration failed:', error);
                });
            });
        }

        function toggleCalculations() {
            const calculationsDiv = document.getElementById('calculations');
            if (calculationsDiv.style.display === 'none' || calculationsDiv.style.display === '') {
                calculationsDiv.style.display = 'block';
            } else {
                calculationsDiv.style.display = 'none';
            }
        }

        function calculateBolus() {
            const currentBloodGlucose = parseFloat(document.getElementById('currentBloodGlucose').value);
            const carbs = parseFloat(document.getElementById('carbs').value);
            const targetBloodGlucose = parseFloat(document.getElementById('targetBloodGlucose').value);
            const cf = parseFloat(document.getElementById('cf').value);
            const icr = parseFloat(document.getElementById('icr').value);

            if (isNaN(currentBloodGlucose) || isNaN(carbs)) {
                document.getElementById('result').innerText = 'Please enter valid values for Current Blood Glucose and Carbs.';
                return;
            }

            const carbBolus = carbs / icr;
            const correctionBolus = cf > 0 ? (currentBloodGlucose - targetBloodGlucose) / cf : 0;
            const totalBolus = carbBolus + correctionBolus;
            const roundedBolus = Math.round(totalBolus * 2) / 2;

            if (roundedBolus <= 0) {
                document.getElementById('result').innerText = 'No insulin bolus is needed.';
            } else {
                document.getElementById('result').innerText = `Total Bolus: ${totalBolus.toFixed(2)} units
Rounded Total Bolus: ${roundedBolus.toFixed(1)} units`;
            }

            const calculations = `Carb Bolus Calculation: (Carbs in Meal / ICR) = (${carbs} / ${icr}) = ${carbBolus.toFixed(2)} units

Correction Bolus Calculation: (Current Blood Glucose - Target Blood Glucose) / CF = (${currentBloodGlucose} - ${targetBloodGlucose}) / ${cf} = ${correctionBolus.toFixed(2)} units

Total Bolus: ${totalBolus.toFixed(2)} units
Rounded Total Bolus: ${roundedBolus.toFixed(1)} units`;
            document.getElementById('calculations').innerText = calculations;
        }
    </script>
</body>
</html>
