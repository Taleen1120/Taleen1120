<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Weather, Translation & Currency Exchange</title>
    <style>
        body {
            font-family: 'Verdana', sans-serif;
            background-color: #f0f9fc;
            color: #333;
            margin: 0;
            padding: 20px;
        }
        header {
            background: linear-gradient(135deg, #004d40, #00796b);
            padding: 30px;
            text-align: center;
            border-radius: 15px;
            color: #fff;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.2);
            margin-bottom: 30px;
        }
        .container {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
        }
        section {
            background-color: #ffffff;
            padding: 20px;
            border-radius: 12px;
            box-shadow: 0 8px 15px rgba(0, 0, 0, 0.1);
            flex: 1 1 300px;
            max-width: 350px;
            margin-bottom: 30px;
            text-align: center;
        }
        section h2 {
            color: #004d40;
            margin-bottom: 15px;
            font-size: 1.6em;
        }
        button, input, select, textarea {
            width: calc(100% - 30px);
            padding: 12px;
            margin: 10px;
            border-radius: 8px;
            font-size: 1em;
        }
        button {
            background-color: #00796b;
            color: #fff;
            border: none;
            cursor: pointer;
            font-weight: bold;
        }
        button:hover {
            background-color: #004d40;
        }
    </style>
</head>
<body>

<header>
    <h1>Weather, Translation & Currency Exchange</h1>
</header>

<div class="container">
    <section id="weather">
        <h2>Weather</h2>
        <p>Select a city to get the temperature:</p>
        <button onclick="getWeather('Mecca')">Mecca</button>
        <button onclick="getWeather('New York')">New York</button>
        <button onclick="getWeather('Beijing')">Beijing</button>
        <button onclick="getWeather('Paris')">Paris</button>
        <p id="weatherResult"></p>
    </section>

    <section id="translation">
        <h2>Translation</h2>
        <textarea id="textToTranslate" rows="3" placeholder="Enter text to translate"></textarea>
        <select id="languageFrom">
            <option value="en">English</option>
            <option value="ar">Arabic</option>
            <option value="es">Spanish</option>
        </select>
        <select id="languageTo">
            <option value="en">English</option>
            <option value="ar">Arabic</option>
            <option value="es">Spanish</option>
        </select>
        <button onclick="translateText()">Translate</button>
        <p id="translationResult"></p>
    </section>

    <section id="currency">
        <h2>Currency Exchange</h2>
        <input type="number" id="amount" placeholder="Amount" min="0" required>
        <input type="text" id="currencyFrom" placeholder="From (e.g., USD)" required>
        <input type="text" id="currencyTo" placeholder="To (e.g., EUR)" required>
        <button onclick="convertCurrency()">Convert</button>
        <p id="currencyResult"></p>
    </section>
</div>

<script>
    function getWeather(city) {
        let temperature;
        switch (city) {
            case 'Mecca':
                temperature = 35;
                break;
            case 'New York':
                temperature = 28;
                break;
            case 'Beijing':
                temperature = 19;
                break;
            case 'Paris':
                temperature = 16;
                break;
            default:
                temperature = 'N/A';
        }
        document.getElementById('weatherResult').innerText = `Temperature in ${city}: ${temperature}°C`;
    }

    async function translateText() {
        const text = document.getElementById('textToTranslate').value;
        const fromLanguage = document.getElementById('languageFrom').value;
        const toLanguage = document.getElementById('languageTo').value;

        if (text === "") {
            alert("Please enter text to translate.");
            return;
        }

        const url = `https://translate.googleapis.com/translate_a/single?client=gtx&sl=${fromLanguage}&tl=${toLanguage}&dt=t&q=${encodeURIComponent(text)}`;

        try {
            const response = await fetch(url);
            const data = await response.json();
            const translatedText = data[0][0][0];
            document.getElementById('translationResult').innerText = translatedText;
        } catch (error) {
            document.getElementById('translationResult').innerText = "Error occurred during translation.";
        }
    }

    async function convertCurrency() {
        const amount = document.getElementById('amount').value;
        const from = document.getElementById('currencyFrom').value.toUpperCase();
        const to = document.getElementById('currencyTo').value.toUpperCase();

        if (!amount || !from || !to) {
            alert("Please fill all fields.");
            return;
        }

        try {
            const response = await fetch(`https://api.exchangerate-api.com/v4/latest/${from}`);
            if (!response.ok) {
                throw new Error("Network response was not ok");
            }
            const data = await response.json();
            const rate = data.rates[to];

            if (rate) {
                const convertedAmount = (amount * rate).toFixed(2);
                document.getElementById('currencyResult').innerText = `${amount} ${from} = ${convertedAmount} ${to}`;
            } else {
                document.getElementById('currencyResult').innerText = `Unable to convert from ${from} to ${to}.`;
            }
        } catch (error) {
            document.getElementById('currencyResult').innerText = "Error occurred during currency conversion.";
        }
    }
</script>

</body>
</html>
