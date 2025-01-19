<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>حاسب عمرك</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background: linear-gradient(45deg, #f0f0f0, #ffffff);
            background-size: 400% 400%;
            animation: gradientBG 10s ease infinite;
            margin: 0;
            height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-direction: column;
        }
        @keyframes gradientBG {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }
        .container {
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            text-align: center;
            width: 100%;
            max-width: 350px;
            height: 95vh;
            animation: fadeIn 1s ease-out;
        }
        @keyframes fadeIn {
            from {
                opacity: 0;
            }
            to {
                opacity: 1;
            }
        }
        h1 {
            color: #00f;
            font-size: 80px;
            margin-bottom: 20px;
        }
        label {
            font-size: 14px;
            color: #555;
            margin-bottom: 8px;
            display: block;
        }
        input[type="date"], select {
            width: 80%;
            padding: 12px;
            font-size: 16px;
            margin-bottom: 15px;
            border: 1px solid #ddd;
            border-radius: 8px;
            outline: none;
            display: block;
            margin-left: auto;
            margin-right: auto;
            background-color: #f9f9f9;
            transition: background-color 0.3s ease, border 0.3s ease;
        }
        input[type="date"]:focus {
            background-color: #e6f7ff;
            border: 1px solid #00f;
        }
        button {
            background-color: #00f;
            color: white;
            font-size: 16px;
            padding: 8px 15px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            width: 100%;
            transition: background-color 0.3s ease;
        }
        button:hover {
            background-color: #0066cc;
        }
        p {
            font-size: 18px;
            color: #333;
            margin-top: 15px;
        }
        .error {
            color: #f44336;
        }
        .motivational {
            font-size: 14px;
            font-weight: bold;
            margin-top: 10px;
        }
        canvas {
            margin-top: 15px;
        }

        /* تعديل مكان الزر ليكون دائري */
        #languageButton {
            background-color: #00f;
            color: white;
            font-size: 10px;  /* تغيير حجم الخط داخل الزر */
            padding: 10px 20px;  /* لجعل الحواف أكبر قليلاً */
            border: none;
            border-radius: 50%;  /* الزر يصبح دائريًا */
            cursor: pointer;
            width: 50px;  /* عرض الزر */
            height: 50px;  /* ارتفاع الزر */
            transition: background-color 0.3s ease;
            position: absolute;
            left: 20px;  /* وضعه على اليسار */
            top: 20px;   /* المسافة من أعلى الصفحة */
            display: flex;
            justify-content: center;
            align-items: center;
            text-align: center;
        }

        #languageButton:hover {
            background-color: #0066cc;
        }
    </style>
</head>
<body>
    <button id="languageButton" onclick="toggleLanguage()"> تغيير اللغة </button>

    <div class="container">
        <h1 id="greetingText">Hello</h1>

        <label for="birthDate" id="birthDateLabel">Select your birth date:</label>
        <input type="date" id="birthDate" name="birthDate">

        <button onclick="calculateAge()"> Let's go </button>

        <p id="result"></p>
        <p id="motivationalMessage" class="motivational"></p>
        <p id="birthdayCountdown"></p>
        <p id="funFacts"></p>

        <canvas id="ageChart" width="400" height="200"></canvas>
    </div>

    <audio id="clickSound" src="click.mp3" preload="auto"></audio>

    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <script>
        let language = 'en'; // Default language

        // Toggle between Arabic and English language
        function toggleLanguage() {
            if (language === 'en') {
                language = 'ar';
                document.getElementById('languageButton').textContent = 'تغيير اللغة';
                document.getElementById('greetingText').textContent = 'مرحباً';
                document.getElementById('birthDateLabel').textContent = 'اختر تاريخ ميلادك:';
                document.querySelector('button').textContent = 'تغيير اللغه ';
            } else {
                language = 'en';
                document.getElementById('languageButton').textContent = 'Change Language';
                document.getElementById('greetingText').textContent = 'Hello';
                document.getElementById('birthDateLabel').textContent = 'Select your birth date:';
                document.querySelector('button').textContent = 'Calculate Age';
            }
        }

        // Function to get motivational message based on age
        function getMotivationalMessage(age) {
            if (age < 20) {
                return language === 'en' ? "You're at the beginning of your life! Enjoy it!" : "أنت في بداية حياتك! استمتع بها!";
            } else if (age < 40) {
                return language === 'en' ? "You're at the best stage of life!" : "أنت في أفضل مراحل الحياة!";
            } else {
                return language === 'en' ? "A lot of time has passed, enjoy every moment!" : "لقد مر وقت طويل، استمتع بكل لحظة!";
            }
        }

        // Function to get fun facts about age
        function getFunFacts() {
            const today = new Date();
            const birthDate = new Date(document.getElementById('birthDate').value);
            const timeDiff = today - birthDate;
            const daysLived = Math.floor(timeDiff / (1000 * 60 * 60 * 24));
            const hoursLived = Math.floor(timeDiff / (1000 * 60 * 60));
            return language === 'en' ? You have lived for ${daysLived} days and ${hoursLived} hours! : لقد عشت لمدة ${daysLived} يومًا و ${hoursLived} ساعة!;
        }

        // Function to countdown until next birthday
        function countdownToNextBirthday(birthDate) {
            const today = new Date();
            let nextBirthday = new Date(birthDate);
            nextBirthday.setFullYear(today.getFullYear());

            if (today > nextBirthday) {
                nextBirthday.setFullYear(today.getFullYear() + 1);
            }

            const timeDiff = nextBirthday - today;
            const daysLeft = Math.floor(timeDiff / (1000 * 60 * 60 * 24));
            return daysLeft;
        }

        // Function to play sound on button click
        function playSound() {
            document.getElementById('clickSound').play();
        }

        // Function to display the age chart
        function displayChart(age) {
            var ctx = document.getElementById('ageChart').getContext('2d');
            var chart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: [language === 'en' ? 'Your Age' : 'عمرك'],
                    datasets: [{
                        label: language === 'en' ? 'Age' : 'السن',
                        data: [age],
                        backgroundColor: 'rgba(75, 192, 192, 0.2)',
                        borderColor: 'rgba(75, 192, 192, 1)',
                        borderWidth: 1
                    }]
                }
            });
        }

        // Function to calculate age and display results
        function calculateAge() {
            playSound();
            const birthDate = document.getElementById('birthDate').value;
            if (!birthDate) {
                document.getElementById('result').textContent = language === 'en' ? "Please select your birth date" : "من فضلك اختر تاريخ ميلادك";
                document.getElementById('result').classList.add("error");
                return;
            }

            const birth = new Date(birthDate);
            const today = new Date();
            let age = today.getFullYear() - birth.getFullYear();
            const monthDifference = today.getMonth() - birth.getMonth();

            if (monthDifference < 0 || (monthDifference === 0 && today.getDate() < birth.getDate())) {
                age--;
            }

            const monthsLived = (today.getFullYear() - birth.getFullYear()) * 12 + (today.getMonth() - birth.getMonth());
            const weeksLived = Math.floor((today - birth) / (1000 * 60 * 60 * 24 * 7));
            const daysLived = Math.floor((today - birth) / (1000 * 60 * 60 * 24));
            const hoursLived = Math.floor((today - birth) / (1000 * 60 * 60));
            const minutesLived = Math.floor((today - birth) / (1000 * 60));

            const message = getMotivationalMessage(age);
            document.getElementById('result').textContent = language === 'en' ? Your age is: ${age} years. : عمرك هو: ${age} سنة.;
            document.getElementById('motivationalMessage').textContent = message;
            document.getElementById('funFacts').textContent = language === 'en' 
                ? You have lived for ${daysLived} days, ${weeksLived} weeks, ${monthsLived} months, ${hoursLived} hours, and ${minutesLived} minutes!
                : لقد عشت لمدة ${daysLived} يومًا، ${weeksLived} أسبوعًا، ${monthsLived} شهرًا، ${hoursLived} ساعة، و ${minutesLived} دقيقة!;

            const daysUntilNextBirthday = countdownToNextBirthday(birthDate);
            document.getElementById('birthdayCountdown').textContent = language === 'en' 
                ? Days until your next birthday: ${daysUntilNextBirthday} days. 
                : الأيام المتبقية حتى عيد ميلادك القادم: ${daysUntilNextBirthday} يوم.;

            displayChart(age);
        }
    </script>
</body>
</html>
