<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Quiz l'ahssan iman</title>
    <style>
        body {
            background-color: #fff0f3;
            font-family: 'Segoe UI', Tahoma, sans-serif;
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            overflow: hidden;
            text-align: center;
        }

        .card {
            background: white;
            padding: 25px;
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.1);
            max-width: 400px;
            width: 85%;
            z-index: 10;
        }

        h1 { color: #ff4d6d; font-size: 1.5rem; margin-bottom: 20px; }
        
        .options-container {
            display: flex;
            flex-direction: column;
            gap: 10px;
        }

        .opt-btn {
            background-color: #f8bbd0;
            color: #ad1457;
            border: 2px solid transparent;
            padding: 12px;
            font-size: 1.1rem;
            border-radius: 12px;
            cursor: pointer;
            transition: 0.3s;
            font-weight: bold;
        }

        .opt-btn:hover { background-color: #ff4d6d; color: white; }

        #game-section, #final-msg { display: none; }
        
        #btn-yes { background-color: #4caf50; color: white; padding: 15px 30px; border-radius: 10px; border: none; font-weight: bold; cursor: pointer;}
        #btn-no { background-color: #f44336; color: white; padding: 15px 30px; border-radius: 10px; border: none; font-weight: bold; cursor: pointer; position: absolute; }

        .heart { position: fixed; top: -10%; font-size: 24px; animation: fall linear forwards; }
        @keyframes fall { to { transform: translateY(110vh); } }
    </style>
</head>
<body>

    <!-- SECTION QUIZ -->
    <div class="card" id="quiz-section">
        <h1 id="question-text">Ra9m li 3ziz 3liya? 🤔</h1>
        <div class="options-container" id="options">
            <!-- Les boutons s'affichent ici via JS -->
        </div>
    </div>

    <!-- SECTION JEU -->
    <div class="card" id="game-section">
        <h1>Barafooo! Saliti l-quiz! 🥳</h1>
        <p>Daba goli liya b-saraha, wash katbghini?</p>
        <div style="height: 120px; position: relative; margin-top: 20px;">
            <button id="btn-yes" onclick="showFinal()">OUI ! 😍</button>
            <button id="btn-no" onmouseover="moveButton()">LA 😜</button>
        </div>
    </div>

    <!-- SECTION FINALE -->
    <div id="final-msg">
        <h1 style="color: #ff4d6d; font-size: 2.5rem;">bghit ngolik ohibok walakin atsselkhini! ❤️</h1>
        <p style="font-size: 1.5rem;">zbeltha wa9ila la??.</p>
        <img src="https://media0.giphy.com/media/v1.Y2lkPTc5MGI3NjExcHV5aDU5bGF3NTJ6dHl6cGd6NXMxZnJ1MmwxNnphemc4MTByMGk2diZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/fUQ4rhUZJYiQsas6WD/giphy.gif" width="200">
    </div>

    <script>
        const questions = [
            {
                q: "Chno hwa ra9m li 3ziz 3liya? 🔢",
                options: ["10", "7", "9"],
                correct: "7"
            },
            {
                q: "Chkon hwa l-la3ib li 3ziz 3liya? ⚽",
                options: ["Messi", "Ronaldo", "Ziyech"],
                correct: "Ronaldo"
            },
            {
                q: "Ina nhar dzadit fih? 🎂",
                options: ["1 chher 1", "20 chher 5", "7 chher 2"],
                correct: "7 chher 2"
            },
            {
                q: "Achnou ktar haja kat3jbni fik? 😍",
                options: ["Dhika dialek", "L-mousiba li fik", "Kolchi fik"],
                correct: "ANY" // N'importe quel choix marche ici
            }
        ];

        let currentQ = 0;

        function loadQuestion() {
            const qObj = questions[currentQ];
            document.getElementById('question-text').innerText = qObj.q;
            const container = document.getElementById('options');
            container.innerHTML = "";
            
            qObj.options.forEach(opt => {
                const btn = document.createElement('button');
                btn.innerText = opt;
                btn.classList.add('opt-btn');
                btn.onclick = () => check(opt);
                container.appendChild(btn);
            });
        }

        function check(choice) {
            const correct = questions[currentQ].correct;
            if (choice === correct || correct === "ANY") {
                currentQ++;
                if (currentQ < questions.length) {
                    loadQuestion();
                } else {
                    document.getElementById('quiz-section').style.display = "none";
                    document.getElementById('game-section').style.display = "block";
                }
            } else {
                alert("Laaaa! Jarbi tani 😂");
            }
        }

        function moveButton() {
            const btnNo = document.getElementById('btn-no');
            const x = Math.random() * (window.innerWidth - 100);
            const y = Math.random() * (window.innerHeight - 50);
            btnNo.style.position = 'fixed';
            btnNo.style.left = x + 'px';
            btnNo.style.top = y + 'px';
            btnNo.innerText = "Ghir nsay!";
        }

        function showFinal() {
            document.getElementById('game-section').style.display = "none";
            document.getElementById('final-msg').style.display = "block";
            setInterval(createHeart, 200);
        }

        function createHeart() {
            const heart = document.createElement('div');
            heart.classList.add('heart');
            heart.innerHTML = '❤️';
            heart.style.left = Math.random() * 100 + 'vw';
            heart.style.animationDuration = Math.random() * 2 + 2 + 's';
            document.body.appendChild(heart);
            setTimeout(() => heart.remove(), 4000);
        }

        // Démarrage
        loadQuestion();
    </script>
</body>
</html>
