<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>Imane World  ❤️</title>
    <style>
        body { margin: 0; background: #000; overflow: hidden; font-family: 'Arial', sans-serif; touch-action: none; }
        canvas { display: block; }
        
        #leaderboard {
            position: fixed; top: 15px; right: 15px;
            background: rgba(255, 0, 85, 0.2); border: 2px solid #ff0055;
            color: white; padding: 12px; border-radius: 12px;
            min-width: 150px; z-index: 100; box-shadow: 0 0 15px #ff0055;
        }
        #leaderboard h3 { margin: 0 0 8px 0; color: #fff; font-size: 16px; text-align: center; text-shadow: 0 0 5px #ff0055; }
        .rank-item { display: flex; justify-content: space-between; margin-bottom: 4px; font-weight: bold; }
        .rank-imane { color: #00f2ff; text-shadow: 0 0 8px #00f2ff; }

        #score-ui {
            position: fixed; top: 15px; left: 15px; color: #fff; 
            font-size: 22px; font-weight: bold; z-index: 100;
            background: #ff0055; padding: 8px 20px; border-radius: 30px;
            box-shadow: 0 0 15px #ff0055;
        }

        .overlay {
            position: fixed; inset: 0; background: #000;
            display: flex; flex-direction: column; justify-content: center; align-items: center; z-index: 200;
            color: white; text-align: center;
        }
        .btn {
            background: #ff0055; border: none; padding: 18px 50px;
            color: white; font-size: 22px; border-radius: 50px; cursor: pointer; margin-top: 20px;
            font-weight: bold; box-shadow: 0 0 30px #ff0055;
        }
    </style>
</head>
<body>

    <div id="start-overlay" class="overlay">
        <h1 style="color: #ff0055; font-size: 3rem; text-shadow: 0 0 20px #ff0055;">IMANE WORLD 👑</h1>
        <p style="font-size: 1.2rem;">Ghalbini a mchichti 🌵!</p>
        <button class="btn" onclick="startGame()">YALLAH START 😍</button>
    </div>

    <div id="game-over" class="overlay" style="display:none;">
        <h1 style="color: #ff0055; font-size: 3rem;">GAME OVER awdi yawdi 😂</h1>
        <button class="btn" onclick="location.reload()">RE-START</button>
    </div>

    <div id="score-ui">SCORE: <span id="score-val">20</span></div>

    <div id="leaderboard">
        <h3>Classement 🏆</h3>
        <div id="rank-list"></div>
    </div>

    <canvas id="gameCanvas"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        let MAP_SIZE = 3500;
        let gameActive = false;
        let player, bots = [], foods = [], bgTexts = [];
        let camera = { x: 0, y: 0 };
        let mouse = { x: window.innerWidth / 2, y: window.innerHeight / 2 };

        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        function initGame() {
            player = { name: "Imane 👑", body: [], color: "#ff0055", isPlayer: true };
            for(let i=0; i<20; i++) player.body.push({x: MAP_SIZE/2, y: MAP_SIZE/2});

            const names = ["Rayan 🤖", "Ajeddig 🌵", "rayan", "rayan", "anaaaaaaa"];
            bots = names.map(name => {
                let bx = Math.random()*MAP_SIZE, by = Math.random()*MAP_SIZE;
                let bBody = []; for(let i=0; i<15; i++) bBody.push({x: bx, y: by});
                return { name, body: bBody, angle: Math.random()*7, speed: 4, color: "#444" };
            });

            foods = []; for(let i=0; i<100; i++) foods.push({x: Math.random()*MAP_SIZE, y: Math.random()*MAP_SIZE});

            // BACKGROUND TEXTS - TRÈS VISIBLES
            const phrases = ["IMANE ❤️", "la tohibini", "Mchichti", "QUEEN"];
            bgTexts = [];
            for(let i=0; i<60; i++) {
                bgTexts.push({
                    x: Math.random()*MAP_SIZE, y: Math.random()*MAP_SIZE,
                    txt: phrases[Math.floor(Math.random()*phrases.length)],
                    size: Math.random()*40 + 50,
                    opacity: Math.random() * 0.5 + 0.3 // Opacité beaucoup plus forte
                });
            }
        }

        function startGame() {
            initGame();
            document.getElementById('start-overlay').style.display = 'none';
            gameActive = true;
            update();
        }

        window.addEventListener('mousemove', e => { mouse.x = e.clientX; mouse.y = e.clientY; });
        window.addEventListener('touchmove', e => { mouse.x = e.touches.clientX; mouse.y = e.touches.clientY; });

        function update() {
            if (!gameActive) return;
            let h = player.body[0];
            camera.x = h.x - canvas.width/2; camera.y = h.y - canvas.height/2;

            let angle = Math.atan2(mouse.y - canvas.height/2, mouse.x - canvas.width/2);
            let nH = { x: h.x + Math.cos(angle)*7, y: h.y + Math.sin(angle)*7 };
            player.body.unshift(nH);
            
            let ate = false;
            for(let i=0; i<foods.length; i++) {
                if(Math.hypot(nH.x-foods[i].x, nH.y-foods[i].y) < 45) {
                    foods[i] = {x: Math.random()*MAP_SIZE, y: Math.random()*MAP_SIZE};
                    ate = true; break;
                }
            }
            if(!ate) player.body.pop();
            document.getElementById('score-val').innerText = player.body.length;

            bots.forEach(bot => {
                let bh = bot.body[0];
                bot.angle += (Math.random()-0.5)*0.3;
                let nBh = { x: bh.x + Math.cos(bot.angle)*bot.speed, y: bh.y + Math.sin(bot.angle)*bot.speed };
                if(nBh.x<0 || nBh.x>MAP_SIZE) bot.angle = Math.PI - bot.angle;
                if(nBh.y<0 || nBh.y>MAP_SIZE) bot.angle = -bot.angle;
                bot.body.unshift(nBh);
                let bAte = false;
                for(let i=0; i<foods.length; i++){
                    if(Math.hypot(nBh.x-foods[i].x, nBh.y-foods[i].y)<45){
                        foods[i] = {x: Math.random()*MAP_SIZE, y: Math.random()*MAP_SIZE};
                        bAte=true; break;
                    }
                }
                if(!bAte) bot.body.pop();
                if(Math.hypot(nH.x-nBh.x, nH.y-nBh.y) < 25) { 
                    gameActive = false; document.getElementById('game-over').style.display = 'flex'; 
                }
            });

            draw();
            updateLeaderboard();
            requestAnimationFrame(update);
        }

        function draw() {
            ctx.fillStyle = '#000'; ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.save(); ctx.translate(-camera.x, -camera.y);

            // BACKGROUND - TEXTES FLASHY
            ctx.textAlign = "center";
            bgTexts.forEach(m => {
                ctx.fillStyle = `rgba(255, 255, 255, ${m.opacity})`; // Blanc brillant
                ctx.font = `bold ${m.size}px Arial`;
                ctx.fillText(m.txt, m.x, m.y);
                // Ombre rose pour l'effet néon
                ctx.fillStyle = `rgba(255, 0, 85, ${m.opacity - 0.1})`;
                ctx.fillText(m.txt, m.x + 3, m.y + 3);
            });

            ctx.font = '40px Arial';
            foods.forEach(f => ctx.fillText('❤️', f.x, f.y));

            bots.forEach(bot => {
                bot.body.forEach((p, i) => {
                    ctx.fillStyle = i === 0 ? "#fff" : "#444";
                    ctx.beginPath(); ctx.arc(p.x, p.y, 15, 0, Math.PI*2); ctx.fill();
                });
                ctx.fillStyle = "#fff"; ctx.font = "bold 18px Arial";
                ctx.fillText(bot.name, bot.body[0].x, bot.body[0].y - 30);
            });

            player.body.forEach((p, i) => {
                ctx.fillStyle = i === 0 ? '#fff' : '#ff0055';
                ctx.beginPath(); ctx.arc(p.x, p.y, Math.max(10, 22 - i*0.4), 0, Math.PI*2); ctx.fill();
                if(i === 0) { ctx.font = '35px Arial'; ctx.fillText('👑', p.x, p.y - 35); }
            });
            ctx.restore();
        }

        function updateLeaderboard() {
            let all = [player, ...bots].sort((a, b) => b.body.length - a.body.length);
            let html = "";
            all.forEach((item, index) => {
                let cls = item.isPlayer ? "rank-imane" : "";
                html += `<div class="rank-item ${cls}"><span>${index+1}. ${item.name}</span><span>${item.body.length}</span></div>`;
            });
            document.getElementById('rank-list').innerHTML = html;
        }
    </script>
</body>
</html>
