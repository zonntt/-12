
<!DOCTYPE html>
<html lang="uk">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Калькулятор PayDay</title>
    <link href="https://fonts.googleapis.com/css2?family=Comfortaa:wght@400;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Comfortaa', sans-serif;
            background: linear-gradient(135deg, #74b9ff, #0984e3);
            color: #fff;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            flex-direction: column;
        }
        .calculator, .login-form {
            background: #fff;
            color: #333;
            border-radius: 12px;
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.2);
            padding: 30px;
            max-width: 400px;
            text-align: center;
            width: 90%;
        }
        h1 {
            font-size: 24px;
            margin-bottom: 20px;
        }
        input, select, button {
            width: 100%;
            padding: 10px;
            margin: 10px 0;
            border: 1px solid #ccc;
            border-radius: 8px;
            font-size: 16px;
        }
        button {
            background: #0984e3;
            color: #fff;
            border: none;
            cursor: pointer;
            transition: background 0.3s;
        }
        button:hover {
            background: #74b9ff;
        }
        .footer {
            margin-top: 30px;
            font-size: 14px;
            color: #fff;
            text-align: center;
        }
    </style>
</head>
<body>
    <!-- Форма для входа -->
    <div class="login-form" id="login-form">
        <h1>Вхід на сайт</h1>
        <input type="email" id="email" placeholder="Email" required>
        <input type="password" id="password" placeholder="Пароль" required>
        <button onclick="login()">Вхід</button>
        <div id="error-message" style="color: red; margin-top: 10px;"></div>
    </div>

    <!-- Калькулятор (скрыт до авторизации) -->
    <div class="calculator" id="calculator" style="display: none;">
        <h1>Калькулятор депозиту</h1>
        <label for="amount">Сума (₴):</label>
        <input type="number" id="amount" placeholder="Максимум: 5000000" max="5000000" step="1">
        <small>Введіть суму до 5000000</small>

        <label for="options">Оберіть PayDay:</label>
        <select id="options">
            <option value="0.06" data-paydays="5">5 PayDay - 0.06%</option>
            <option value="0.15" data-paydays="10">10 PayDay - 0.15%</option>
            <option value="0.3" data-paydays="20">20 PayDay - 0.3%</option>
            <option value="0.8" data-paydays="60">60 PayDay - 0.8%</option>
        </select>
        
        <button onclick="calculate()">Розрахувати</button>
        
        <div class="result" id="result"></div>
    </div>

    <div class="footer">
        <p>Designed by Kirill Phantom</p>
    </div>

    <!-- Firebase SDK -->
    <script type="module">
        // Импортирование модулей Firebase
        import { initializeApp } from "https://www.gstatic.com/firebasejs/9.9.1/firebase-app.js";
        import { getAuth, signInWithEmailAndPassword } from "https://www.gstatic.com/firebasejs/9.9.1/firebase-auth.js";

        // Конфигурация Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyByAY6msOFIZpfco-cBt26L0rRDw3grh2w",
            authDomain: "depozit-ug-team.firebaseapp.com",
            projectId: "depozit-ug-team",
            storageBucket: "depozit-ug-team.appspot.com",
            messagingSenderId: "357271165821",
            appId: "1:357271165821:web:103e11f694a50a56ce8a14"
        };

        // Инициализация Firebase
        const app = initializeApp(firebaseConfig);
        const auth = getAuth(app);

        // Функция входа
        window.login = function() {
            const email = document.getElementById('email').value;
            const password = document.getElementById('password').value;
            const errorMessage = document.getElementById('error-message');

            signInWithEmailAndPassword(auth, email, password)
                .then((userCredential) => {
                    const user = userCredential.user;
                    document.getElementById('login-form').style.display = 'none';
                    document.getElementById('calculator').style.display = 'block';
                })
                .catch((error) => {
                    const errorCode = error.code;
                    const errorMessageText = error.message;
                    errorMessage.innerText = "Невірний email або пароль.";
                });
        };

        // Функция для расчета
        window.calculate = function() {
            const amountInput = document.getElementById("amount");
            const selectedOption = document.getElementById("options");
            const amount = parseFloat(amountInput.value);
            const rate = parseFloat(selectedOption.value);
            const paydays = parseInt(selectedOption.options[selectedOption.selectedIndex].dataset.paydays, 10);

            if (isNaN(amount) || amount > 5000000 || amount <= 0) {
                document.getElementById("result").innerText = 
                    "Будь ласка, введіть суму до 5000000.";
                return;
            }

            const finalRate = 1.0 + rate / 100;
            const result = amount * Math.pow(finalRate, paydays);

            document.getElementById("result").innerText = 
                `Підсумкова сума: ₴${result.toFixed(2)}`;
        };
    </script>
</body>
</html>
