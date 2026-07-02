<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Kawaii Fast & Cozy</title>
    <style>
        :root {
            --pastel-pink: #ffe5ec;
            --pastel-blue: #d8f3dc;
            --pastel-purple: #f0e6ff;
            --text-color: #6c5b7b;
            --kawaii-pink: #ff758f;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Comic Sans MS', 'Chalkboard SE', sans-serif;
            user-select: none;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            background-color: var(--pastel-pink);
            color: var(--text-color);
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: space-between;
            min-height: 100vh;
            overflow: hidden;
            padding: 10px 0;
            transition: background 1s ease;
        }

        /* HUD Superior */
        .hud {
            display: flex;
            justify-content: space-around;
            width: 100%;
            max-width: 360px;
            background: rgba(255, 255, 255, 0.7);
            padding: 10px;
            border-radius: 20px;
            box-shadow: 0 8px 20px rgba(0,0,0,0.05);
            font-weight: bold;
            font-size: 16px;
            z-index: 10;
        }

        .hud span {
            color: var(--kawaii-pink);
        }

        /* Tela do Jogo */
        #gameCanvas {
            background: #fbf7f5;
            border-radius: 25px;
            border: 6px solid #fff;
            box-shadow: 0 12px 30px rgba(108, 91, 123, 0.15);
            max-width: 92vw;
            max-height: 45vh;
        }

        /* Painel de Controles */
        .controls-layer {
            width: 100%;
            max-width: 380px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px 20px;
            margin-bottom: 5px;
            z-index: 10;
        }

        .steering-box, .pedals-box {
            display: flex;
            gap: 12px;
        }

        .btn-game {
            width: 65px;
            height: 65px;
            border-radius: 50%;
            border: 3px solid #fff;
            font-size: 22px;
            display: flex;
            justify-content: center;
            align-items: center;
            box-shadow: 0 6px 12px rgba(0,0,0,0.08);
            font-weight: bold;
            color: white;
        }

        .btn-left, .btn-right { background: #b5e2fa; color: #4a90e2; }
        .btn-brake { background: #ffb3c1; font-size: 14px; }
        .btn-gas { background: #c1fba4; font-size: 14px; color: #52b788; }

        .btn-game:active {
            transform: scale(0.9);
            filter: brightness(0.95);
        }

        /* TELA DE INÍCIO E GAME OVER SUPER KAWAII */
        .overlay {
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background: linear-gradient(135deg, #fff0f3 0%, #ffe5ec 100%);
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            z-index: 3000;
            text-align: center;
            padding: 20px;
            overflow: hidden;
        }

        /* Elementos de Decoração de Fundo (Nuvens e Estrelas) */
        .decorations {
            position: absolute;
            width: 100%;
            height: 100%;
            pointer-events: none;
            top: 0; left: 0;
        }
        .cloud {
            position: absolute;
            background: #fff;
            border-radius: 50px;
            opacity: 0.85;
            animation: floatClouds 6s ease-in-out infinite alternate;
        }
        .cloud::before, .cloud::after {
            content: ''; position: absolute; background: #fff; border-radius: 50%;
        }
        .c1 { width: 80px; height: 30px; top: 15%; left: 10%; }
        .c1::before { width: 40px; height: 40px; top: -20px; left: 10%; }
        .c1::after { width: 35px; height: 35px; top: -15px; right: 15%; }
        
        .c2 { width: 100px; height: 35px; bottom: 15%; right: 8%; animation-delay: 2s; }
        .c2::before { width: 50px; height: 50px; top: -25px; left: 15%; }
        .c2::after { width: 40px; height: 40px; top: -15px; right: 20%; }

        @keyframes floatClouds {
            0% { transform: translateX(0) translateY(0); }
            100% { transform: translateX(15px) translateY(-5px); }
        }

        /* Título Kawaii Animado */
        .kawaii-title {
            font-size: 34px;
            color: #ff4d6d;
            margin-bottom: 5px;
            text-shadow: 3px 3px 0px #fff, 5px 5px 0px #ffb3c1;
            animation: bounceTitle 2s ease-in-out infinite alternate;
            z-index: 5;
        }

        @keyframes bounceTitle {
            0% { transform: translateY(0) scale(1); }
            100% { transform: translateY(-8px) scale(1.02); }
        }

        .kawaii-sub {
            font-size: 15px;
            color: #8c7a9b;
            margin-bottom: 20px;
            background: rgba(255,255,255,0.6);
            padding: 5px 15px;
            border-radius: 15px;
            z-index: 5;
        }

        /* Moldura da foto de perda */
        .meme-frame {
            width: 210px;
            height: 210px;
            border-radius: 30px;
            border: 8px solid #fff;
            box-shadow: 0 10px 25px rgba(255, 143, 163, 0.3);
            object-fit: cover;
            margin-bottom: 25px;
            z-index: 5;
            transition: transform 0.3s;
        }

        /* Botão Candy Play */
        .btn-restart {
            background: linear-gradient(to bottom, #ff8fa3, #ff758f);
            color: white;
            border: 4px solid #fff;
            padding: 14px 45px;
            font-size: 20px;
            border-radius: 40px;
            font-weight: bold;
            box-shadow: 0 8px 20px rgba(255,117,143,0.4), inset 0 -4px 0px rgba(0,0,0,0.1);
            cursor: pointer;
            z-index: 5;
            text-transform: uppercase;
            letter-spacing: 1px;
            transition: transform 0.1s;
        }

        .btn-restart:active {
            transform: scale(0.95);
            box-shadow: 0 4px 10px rgba(255,117,143,0.4);
        }
    </style>
</head>
<body>

    <div class="hud">
        <div>🛣️ <span id="distance">0.0</span> km</div>
        <div>🪙 <span id="coins">0</span></div>
    </div>

    <canvas id="gameCanvas" width="320" height="420"></canvas>

    <div class="controls-layer">
        <div class="steering-box">
            <div class="btn-game btn-left" id="key-left">◀</div>
            <div class="btn-game btn-right" id="key-right">▶</div>
        </div>
        <div class="pedals-box">
            <div class="btn-game btn-brake" id="key-brake">FREIO</div>
            <div class="btn-game btn-gas" id="key-gas">CORRER</div>
        </div>
    </div>

    <!-- Capa de Início / Game Over -->
    <div id="lostOverlay" class="overlay">
        <div class="decorations">
            <div class="cloud c1"></div>
            <div class="cloud c2"></div>
        </div>
        
        <h1 id="lostTitle" class="kawaii-title">Kawaii Racer ✨</h1>
        <p id="lostSub" class="kawaii-sub">Toque abaixo para iniciar o seu passeio fofo!</p>
        
        <!-- Foto inicial padrão, depois vira o sorteio dos memes -->
        <img id="memeDisplay" class="meme-frame" src="1000163155.jpg" alt="Capa">
        
        <button class="btn-restart" id="startBtn" onclick="startGame()">Vamos Lá! 🌸</button>
    </div>

    <script>
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");

        const memeImages = [
            "1000163185.jpg",
            "1000163183.jpg",
            "1000163161.jpg",
            "1000163155.jpg",
            "1000163209.jpg",
            "1000157959.jpg"
        ];

        let player = { x: 135, y: 330, width: 50, height: 75, targetX: 135 };
        let obstacles = [];
        let coinsList = [];
        let distanceCount = 0;
        let collectedCoins = 0;
        let speed = 2;
        let maxSpeed = 7;
        let gameActive = false;
        let frame = 0;

        let moveLeft = false, moveRight = false, isGas = false, isBrake = false;

        const setupTouch = (id, pressCallback, releaseCallback) => {
            const btn = document.getElementById(id);
            btn.addEventListener("touchstart", (e) => { e.preventDefault(); pressCallback(); });
            btn.addEventListener("touchend", (e) => { e.preventDefault(); releaseCallback(); });
            btn.addEventListener("mousedown", pressCallback);
            btn.addEventListener("mouseup", releaseCallback);
        };

        setupTouch("key-left", () => moveLeft = true, () => moveLeft = false);
        setupTouch("key-right", () => moveRight = true, () => moveRight = false);
        setupTouch("key-gas", () => isGas = true, () => isGas = false);
        setupTouch("key-brake", () => isBrake = true, () => isBrake = false);

        function startGame() {
            document.getElementById("lostOverlay").style.display = "none";
            player.x = 135; player.targetX = 135;
            obstacles = []; coinsList = [];
            distanceCount = 0; collectedCoins = 0; speed = 3;
            document.getElementById("coins").innerText = "0";
            document.getElementById("distance").innerText = "0.0";
            gameActive = true;
            loop();
        }

        function triggerGameOver(reasonText) {
            gameActive = false;
            document.getElementById("lostTitle").innerText = "You lost, what a shame!";
            document.getElementById("lostSub").innerText = reasonText;
            
            const randomMeme = memeImages[Math.floor(Math.random() * memeImages.length)];
            document.getElementById("memeDisplay").src = randomMeme;
            
            document.getElementById("startBtn").innerText = "Recomeçar 🌟";
            document.getElementById("lostOverlay").style.display = "flex";
        }

        function loop() {
            if (!gameActive) return;
            frame++;
            
            if (isGas && speed < maxSpeed) speed += 0.1;
            else if (isBrake && speed > 1) speed -= 0.15;
            else if (speed > 2) speed -= 0.03;

            distanceCount += (speed * 0.001);
            document.getElementById("distance").innerText = distanceCount.toFixed(2);

            if (distanceCount < 1.0) {
                document.body.style.backgroundColor = "#ffe5ec"; 
            } else if (distanceCount < 2.5) {
                document.body.style.backgroundColor = "#e8dbfc"; 
            } else {
                document.body.style.backgroundColor = "#fceade"; 
            }

            if (moveLeft && player.x > 30) player.x -= 4;
            if (moveRight && player.x < 240) player.x += 4;

            if (frame % 80 === 0) {
                let laneX = [45, 135, 225][Math.floor(Math.random() * 3)];
                let randType = Math.random();
                
                if (randType < 0.4) {
                    obstacles.push({ x: laneX, y: -80, type: 'cone', w: 40, h: 40 });
                } else if (randType < 0.7) {
                    obstacles.push({ x: laneX, y: -80, type: 'carro', w: 48, h: 75 });
                } else {
                    obstacles.push({ x: laneX, y: -80, type: 'policia', w: 48, h: 75 });
                }
            }

            if (frame % 50 === 0) {
                let laneX = [55, 145, 235][Math.floor(Math.random() * 3)];
                coinsList.push({ x: laneX, y: -40, w: 20, h: 20 });
            }

            for (let i = obstacles.length - 1; i >= 0; i--) {
                obstacles[i].y += speed;
                if (player.x < obstacles[i].x + obstacles[i].w &&
                    player.x + player.width > obstacles[i].x &&
                    player.y < obstacles[i].y + obstacles[i].h &&
                    player.y + player.height > obstacles[i].y) {
                    
                    if (obstacles[i].type === 'policia') {
                        triggerGameOver("Você bateu na polícia e levou uma multa de trânsito!");
                    } else {
                        triggerGameOver("Oh não! Você bateu num obstáculo fofinho!");
                    }
                    return;
                }
                if (obstacles[i].y > canvas.height) obstacles.splice(i, 1);
            }

            for (let i = coinsList.length - 1; i >= 0; i--) {
                coinsList[i].y += speed;
                if (player.x < coinsList[i].x + coinsList[i].w &&
                    player.x + player.width > coinsList[i].x &&
                    player.y < coinsList[i].y + coinsList[i].h &&
                    player.y + player.height > coinsList[i].y) {
                    
                    collectedCoins++;
                    document.getElementById("coins").innerText = collectedCoins;
                    coinsList.splice(i, 1);
                    continue;
                }
                if (coinsList[i].y > canvas.height) coinsList.splice(i, 1);
            }

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Estrada
            ctx.fillStyle = "#f2ece9";
            ctx.fillRect(20, 0, 280, canvas.height);
            
            ctx.strokeStyle = "#fff";
            ctx.lineWidth = 4;
            ctx.setLineDash([15, 15]);
            ctx.beginPath();
            ctx.moveTo(110, 0); ctx.lineTo(110, canvas.height);
            ctx.moveTo(210, 0); ctx.lineTo(210, canvas.height);
            ctx.stroke();

            // Moedas
            coinsList.forEach(c => {
                ctx.fillStyle = "#ffd166";
                ctx.strokeStyle = "#f7a072";
                ctx.lineWidth = 3;
                ctx.setLineDash([]);
                ctx.beginPath();
                ctx.arc(c.x + 10, c.y + 10, 10, 0, Math.PI * 2);
                ctx.fill();
                ctx.stroke();
            });

            // Obstáculos
            obstacles.forEach(o => {
                ctx.setLineDash([]);
                if (o.type === 'cone') {
                    ctx.fillStyle = "#ffb703"; 
                    ctx.beginPath();
                    ctx.moveTo(o.x + 20, o.y);
                    ctx.lineTo(o.x, o.y + 40);
                    ctx.lineTo(o.x + 40, o.y + 40);
                    ctx.fill();
                } else if (o.type === 'carro') {
                    ctx.fillStyle = "#bbf2f6"; 
                    ctx.beginPath(); ctx.roundRect(o.x, o.y, o.w, o.h, 12); ctx.fill();
                } else if (o.type === 'policia') {
                    ctx.fillStyle = "#6c5b7b"; 
                    ctx.beginPath(); ctx.roundRect(o.x, o.y, o.w, o.h, 12); ctx.fill();
                    ctx.fillStyle = (frame % 20 < 10) ? "#ff4d6d" : "#4a90e2";
                    ctx.fillRect(o.x + 14, o.y + 30, 20, 8);
                }
            });

            // Player
            ctx.fillStyle = "#ff8fa3";
            ctx.setLineDash([]);
            ctx.beginPath();
            ctx.roundRect(player.x, player.y, player.width, player.height, 14);
            ctx.fill();
            ctx.fillStyle = "#fff";
            ctx.fillRect(player.x + 8, player.y + 20, 34, 15);

            requestAnimationFrame(loop);
        }
    </script>
</body>
</html>
