<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Zombie Shooter</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #1a1a1a;
            color: #fff;
            font-family: 'Courier New', Courier, monospace;
        }
        canvas {
            background-color: #333;
            border: 2px solid #fff;
        }
        .info-panel {
            margin-top: 10px;
            font-size: 1.2em;
            text-align: center;
        }
        .stats {
            display: flex;
            gap: 20px;
            justify-content: center;
            margin-bottom: 10px;
        }
        .stat {
            background-color: #555;
            padding: 8px 15px;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <h1>Zombie Shooter</h1>
    <div class="stats">
        <div class="stat">Nyawa: <span id="health">100</span></div>
        <div class="stat">Amunisi: <span id="ammo">10</span></div>
        <div class="stat">Skor: <span id="score">0</span></div>
    </div>
    <canvas id="gameCanvas" width="800" height="600"></canvas>
    <div class="info-panel">
        <p>Gunakan tombol **WASD** untuk bergerak. Klik kiri mouse untuk menembak.</p>
        <p>Tekan tombol **R** untuk mengisi ulang amunisi.</p>
        <p id="gameOverText" style="display:none; color: red;">GAME OVER! Tekan F5 untuk mulai lagi.</p>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const healthDisplay = document.getElementById('health');
        const ammoDisplay = document.getElementById('ammo');
        const scoreDisplay = document.getElementById('score');
        const gameOverText = document.getElementById('gameOverText');

        // ==== Game Variables ====
        let isGameOver = false;
        let score = 0;
        let keys = {};
        const player = {
            x: canvas.width / 2,
            y: canvas.height / 2,
            size: 20,
            color: '#00BFFF',
            speed: 3,
            health: 100,
            ammo: 10,
            maxAmmo: 10
        };
        const zombies = [];
        const bullets = [];
        let lastZombieSpawn = Date.now();
        const zombieSpawnInterval = 1000;
        let lastShootTime = 0;
        const shootCooldown = 300;
        let lastReloadTime = 0;
        const reloadCooldown = 2000; // 2 detik cooldown untuk reload

        // ==== Fungsi Menggambar (Rendering) ====
        function drawPlayer() {
            ctx.fillStyle = player.color;
            ctx.beginPath();
            ctx.arc(player.x, player.y, player.size, 0, Math.PI * 2);
            ctx.fill();
        }

        function drawZombies() {
            zombies.forEach(zombie => {
                ctx.fillStyle = zombie.color;
                ctx.fillRect(zombie.x - zombie.size / 2, zombie.y - zombie.size / 2, zombie.size, zombie.size);
            });
        }

        function drawBullets() {
            bullets.forEach(bullet => {
                ctx.fillStyle = bullet.color;
                ctx.beginPath();
                ctx.arc(bullet.x, bullet.y, bullet.size, 0, Math.PI * 2);
                ctx.fill();
            });
        }

        // ==== Fungsi Pembaruan Logika (Update Logic) ====
        function updatePlayer() {
            if (keys['w'] || keys['W']) player.y -= player.speed;
            if (keys['s'] || keys['S']) player.y += player.speed;
            if (keys['a'] || keys['A']) player.x -= player.speed;
            if (keys['d'] || keys['D']) player.x += player.speed;

            // Batasi pergerakan pemain di dalam canvas
            if (player.x < player.size) player.x = player.size;
            if (player.x > canvas.width - player.size) player.x = canvas.width - player.size;
            if (player.y < player.size) player.y = player.size;
            if (player.y > canvas.height - player.size) player.y = canvas.height - player.size;

            healthDisplay.textContent = player.health;
            ammoDisplay.textContent = player.ammo;
            scoreDisplay.textContent = score;
        }

        function updateZombies() {
            zombies.forEach(zombie => {
                const angle = Math.atan2(player.y - zombie.y, player.x - zombie.x);
                zombie.x += Math.cos(angle) * zombie.speed;
                zombie.y += Math.sin(angle) * zombie.speed;

                // Cek tabrakan zombie dengan pemain
                const distance = Math.hypot(player.x - zombie.x, player.y - zombie.y);
                if (distance < player.size + zombie.size / 2) {
                    player.health -= 1;
                    if (player.health <= 0) {
                        isGameOver = true;
                    }
                }
            });
        }

        function updateBullets() {
            for (let i = bullets.length - 1; i >= 0; i--) {
                const bullet = bullets[i];
                bullet.x += bullet.velocityX;
                bullet.y += bullet.velocityY;

                // Hapus peluru jika keluar dari layar
                if (bullet.x < 0 || bullet.x > canvas.width || bullet.y < 0 || bullet.y > canvas.height) {
                    bullets.splice(i, 1);
                }
            }
        }

        function handleCollisions() {
            for (let i = bullets.length - 1; i >= 0; i--) {
                const bullet = bullets[i];
                for (let j = zombies.length - 1; j >= 0; j--) {
                    const zombie = zombies[j];
                    const distance = Math.hypot(bullet.x - zombie.x, bullet.y - zombie.y);
                    if (distance < bullet.size + zombie.size / 2) {
                        bullets.splice(i, 1);
                        zombies.splice(j, 1);
                        score += 10;
                        break;
                    }
                }
            }
        }

        function spawnZombie() {
            if (Date.now() - lastZombieSpawn > zombieSpawnInterval) {
                let x, y;
                if (Math.random() < 0.5) {
                    x = Math.random() < 0.5 ? 0 : canvas.width;
                    y = Math.random() * canvas.height;
                } else {
                    x = Math.random() * canvas.width;
                    y = Math.random() < 0.5 ? 0 : canvas.height;
                }

                zombies.push({
                    x: x,
                    y: y,
                    size: 25,
                    color: '#228B22',
                    speed: 0.5 + Math.random() * 0.5
                });
                lastZombieSpawn = Date.now();
            }
        }

        // ==== Game Loop Utama ====
        function gameLoop() {
            if (isGameOver) {
                gameOverText.style.display = 'block';
                return;
            }

            ctx.clearRect(0, 0, canvas.width, canvas.height);
            spawnZombie();
            updatePlayer();
            updateZombies();
            updateBullets();
            handleCollisions();
            drawPlayer();
            drawZombies();
            drawBullets();

            requestAnimationFrame(gameLoop);
        }

        // ==== Event Listeners ====
        document.addEventListener('keydown', e => {
            keys[e.key] = true;
        });
        document.addEventListener('keyup', e => {
            keys[e.key] = false;
        });

        canvas.addEventListener('click', e => {
            const currentTime = Date.now();
            if (player.ammo > 0 && currentTime - lastShootTime > shootCooldown) {
                player.ammo--;
                lastShootTime = currentTime;
                
                const rect = canvas.getBoundingClientRect();
                const mouseX = e.clientX - rect.left;
                const mouseY = e.clientY - rect.top;

                const angle = Math.atan2(mouseY - player.y, mouseX - player.x);
                bullets.push({
                    x: player.x,
                    y: player.y,
                    size: 5,
                    color: '#FFD700',
                    velocityX: Math.cos(angle) * 10,
                    velocityY: Math.sin(angle) * 10
                });
            }
        });

        document.addEventListener('keydown', e => {
            const currentTime = Date.now();
            if (e.key === 'r' && player.ammo < player.maxAmmo && currentTime - lastReloadTime > reloadCooldown) {
                player.ammo = player.maxAmmo;
                lastReloadTime = currentTime;
            }
        });

        // Mulai game
        gameLoop();
    </script>
</body>
</html>