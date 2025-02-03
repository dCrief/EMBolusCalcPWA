<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Emerson's Bolus Calculator</title>
    <link rel="manifest" href="manifest.json">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="Bolus Calculator">
    <link rel="apple-touch-icon" href="icon.png">
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            background-color: #f8f9fa;
        }
        .container {
            max-width: 600px;
            margin: auto;
            background: white;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        h1 {
            text-align: center;
            margin-bottom: 20px;
        }
        .form-group {
            margin-bottom: 15px;
        }
        label {
            display: block;
            margin-bottom: 5px;
        }
        input, select, button {
            width: calc(100% - 20px); /* Ensure consistent width */
            padding: 10px;
            font-size: 16px;
            border: 1px solid #ccc;
            border-radius: 5px;
            margin: auto;
        }
        button {
            background-color: #007bff;
            color: white;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
        .result {
            margin-top: 20px;
            font-size: 18px;
            font-weight: bold;
            text-align: center;
        }
        .calculations {
            display: none;
            margin-top: 15px;
            font-size: 14px;
            color: #555;
            background: #f1f1f1;
            padding: 10px;
            border-radius: 5px;
            box-shadow: inset 0 1px 3px rgba(0, 0, 0, 0.1);
        }
    </style>
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
    </script>
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
        <a href="#" onclick="toggleCalculations()" style="display:block; text-align:center; margin-top:15px; font-size:14px;">Show me the Math</a>
        <div class="calculations" id="calculations"></div>
    </div>
</body>
</html>
