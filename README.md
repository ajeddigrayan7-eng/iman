
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Imane Slither Boss ❤️</title>
    <style>
        body { margin: 0; background: #000; overflow: hidden; font-family: 'Arial', sans-serif; touch-action: none; }
        canvas { display: block; }
        
        #ui { position: fixed; top: 10px; left: 10px; color: #fff; z-index: 100; font-weight: bold; background: rgba(255,0,85,0.5); padding: 5px 15px; border-radius: 20px; }
        #leaderboard { position: fixed; top: 10px; right: 10px; background: rgba(0,0,0,0.8); border: 1px solid #ff0055; color: white; padding: 10px; border-radius: 10px; min-width: 120px; z-index: 100; font-size: 12px; }
        
        /* Joystick pour Mobile */
        #joystick-container { position: fixed; bottom: 40px; left: 40px; width: 120px; height: 120px; background: rgba(255,255,255,0.1); border-radius: 50%; z-index: 150; display: flex; align-items: center; justify-content: center; border: 2px solid rgba(255,255,255,0.3); }
        #joystick-knob { width: 50px; height: 50px; background: #ff0055; border-radius: 50%; box-shadow: 0 0 15px #ff0055; }

        .overlay { position: fixed; inset: 0; background: #000; display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 200; color: white; text-align: center; }
        .btn { background: #ff0055; border: none; padding: 15px 40px; color: white; font-size: 20px; border-radius: 50px; cursor: pointer; margin-top: 20px; font-weight: bold; }
    </style>
</head>
<body>

    <div id="start-overlay" class="overlay">
        <h1 style="color: #ff0055; font-size: 2.5rem;">IMANE WORLD 👑</h1>
        <p>Ghalbi Rayan 🤖 ou Ajeddig 🌵!<br>St3mly l-joystick l-t-tehmi</p>
        <button class="btn" onclick="startGame()">START 😍</button>
    </div>

    <div id="game-over" class="overlay" style="display:none;">
        <h1 style="color: #ff0055;">GAME OVER 😂</h1>
        <button class="btn" onclick="location.reload()">RE-START</button>
    </div>

    <div id="ui">Score: <span id="score">0</span></div>
    <div id="leaderboard"><h3>Leaderboard</h3><div id="rank-list"></div></div>
    <div id="joystick-container"><div id="joystick-knob"></div></div>

    <canvas id="gameCanvas"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const MAP_SIZE = 3000;
        let gameActive = false, player, bots = [], foods = [], bgTexts = [];
        let camera = { x: 0, y: 0 }, angle = 0;

        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        function initGame() {
            player = { name: "Imane 👑", body: Array(20).fill({x: MAP_SIZE/2, y: MAP_SIZE/2}), isPlayer: true };
            const botNames = ["Rayan 🤖", "Ajeddig 🌵", "Bot_1", "Bot_2"];
            bots = botNames.map(name => ({
                name, body: Array(15).fill({x: Math.random()*MAP_SIZE, y: Math.random()*MAP_SIZE}),
                angle: Math.random()*6, speed: 3.5, color: "#555"
            }));
            foods = Array(60).fill(0).map(() => ({x: Math.random()*MAP_SIZE, y: Math.random()*MAP_SIZE}));
            const phrases = ["IMANE ❤️", "KAN BGHIK", "MOUSIBA", "QUEEN"];
            bgTexts = Array(40).fill(0).map(() => ({
                x: Math.random()*MAP_SIZE, y: Math.random()*MAP_SIZE,
                txt: phrases[Math.floor(Math.random()*phrases.length)], size: 60
            }));
        }

        // Joystick Logic
        const knob = document.getElementById('joystick-knob');
        window.addEventListener('touchmove', e => {
            const touch = e.touches[0];
            const container = document.getElementById('joystick-container').getBoundingClientRect();
            const centerX = container.left + container.width/2;
            const centerY = container.top + container.height/2;
            const dx = touch.clientX - centerX;
            const dy = touch.clientY - centerY;
            angle = Math.atan2(dy, dx);
            const dist = Math.min(60, Math.hypot(dx, dy));
            knob.style.transform = `translate(${Math.cos(angle)*dist}px, ${Math.sin(angle)*dist}px)`;
        }, {passive: false});

        function startGame() {
            initGame(); document.getElementById('start-overlay').style.display = 'none';
            gameActive = true; update();
        }

        function update() {
            if (!gameActive) return;
            let h = player.body[0];
            camera.x = h.x - canvas.width/2; camera.y = h.y - canvas.height/2;

            let nH = { x: h.x + Math.cos(angle)*6, y: h.y + Math.sin(angle)*6 };
            player.body.unshift(nH);
            
            let ate = false;
            foods.forEach((f, i) => {
                if(Math.hypot(nH.x-f.x, nH.y-f.y) < 40) { foods[i] = {x: Math.random()*MAP_SIZE, y: Math.random()*MAP_SIZE}; ate = true; }
            });
            if(!ate) player.body.pop();

            // Collision avec Bots (Corps entier)
            bots.forEach(bot => {
                bot.angle += (Math.random()-0.5)*0.3;
                let bHead = bot.body[0];
                let nBh = { x: bHead.x + Math.cos(bot.angle)*bot.speed, y: bHead.y + Math.sin(bot.angle)*bot.speed };
                bot.body.unshift(nBh); bot.body.pop();

                bot.body.forEach(p => {
                    if(Math.hypot(nH.x-p.x, nH.y-p.y) < 20) { gameActive = false; document.getElementById('game-over').style.display = 'flex'; }
                });
            });

            draw(); updateLeaderboard();
            requestAnimationFrame(update);
        }

        function draw() {
            ctx.fillStyle = '#000'; ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.save(); ctx.translate(-camera.x, -camera.y);
            ctx.textAlign = "center";
            bgTexts.forEach(m => { ctx.fillStyle = "rgba(255, 255, 255, 0.4)"; ctx.font = `bold ${m.size}px Arial`; ctx.fillText(m.txt, m.x, m.y); });
            ctx.font = '35px Arial'; foods.forEach(f => ctx.fillText('❤️', f.x, f.y));
            bots.forEach(bot => {
                bot.body.forEach(p => { ctx.fillStyle = "#555"; ctx.beginPath(); ctx.arc(p.x, p.y, 14, 0, Math.PI*2); ctx.fill(); });
                ctx.fillStyle = "#fff"; ctx.font = "bold 16px Arial"; ctx.fillText(bot.name, bot.body[0].x, bot.body[0].y - 25);
            });
            player.body.forEach((p, i) => {
                ctx.fillStyle = i === 0 ? '#fff' : '#ff0055';
                ctx.beginPath(); ctx.arc(p.x, p.y, Math.max(10, 20-i*0.4), 0, Math.PI*2); ctx.fill();
                if(i===0) { ctx.font='30px Arial'; ctx.fillText('👑', p.x, p.y-30); }
            });
            ctx.restore();
        }

        function updateLeaderboard() {
            let all = [player, ...bots].sort((a, b) => b.body.length - a.body.length);
            document.getElementById('rank-list').innerHTML = all.map((it, i) => `<div class="rank-item ${it.isPlayer?'rank-imane':''}"><span>${i+1}. ${it.name}</span><span>${it.body.length}</span></div>`).join('');
            document.getElementById('score').innerText = player.body.length;
        }
    </script>
</body>
</html>
