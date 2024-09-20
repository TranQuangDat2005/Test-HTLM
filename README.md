<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>8-bit Black Myth: Wukong - Endless Runner</title>
    <style>
        body {
            margin: 0;
            background-color: #000;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        canvas {
            background-color: #87ceeb; /* Nền xanh da trời */
            image-rendering: pixelated; /* Tạo hiệu ứng 8-bit */
            border: 2px solid #333;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas" width="600" height="400"></canvas>
    <script>
        // Lấy phần tử canvas và ngữ cảnh 2D
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        // Khởi tạo nhân vật khỉ 8-bit
        const wukong = {
            x: 50,
            y: 300,
            size: 30,
            color: 'brown',
            speed: 5,
            dx: 0,
            dy: 0,
            isAttacking: false,
        };

        // Tạo cây
        const trees = [];
        for (let i = 0; i < 5; i++) {
            trees.push({
                x: Math.random() * canvas.width,
                y: 350,
                width: 20,
                height: 50,
                color: 'green',
                speed: 2
            });
        }

        // Tạo quái vật
        const monsters = [];
        for (let i = 0; i < 3; i++) {
            monsters.push({
                x: canvas.width + Math.random() * 300,
                y: 300,
                size: 30,
                color: 'red',
                speed: 3
            });
        }

        // Vẽ nhân vật khỉ
        function drawWukong() {
            ctx.fillStyle = wukong.color;
            // Đầu khỉ
            ctx.fillRect(wukong.x, wukong.y, wukong.size, wukong.size);
            // Thân khỉ
            ctx.fillRect(wukong.x + 10, wukong.y + 30, 10, 20);
            // Tay khỉ
            ctx.fillRect(wukong.x - 5, wukong.y + 30, 40, 5);
            // Chân khỉ
            ctx.fillRect(wukong.x + 5, wukong.y + 50, 5, 10);
            ctx.fillRect(wukong.x + 20, wukong.y + 50, 5, 10);

            if (wukong.isAttacking) {
                drawStick();
            }
        }

        // Vẽ gậy
        function drawStick() {
            ctx.fillStyle = 'goldenrod';
            ctx.fillRect(wukong.x + 35, wukong.y + 30, 30, 5);
        }

        // Vẽ cây
        function drawTrees() {
            trees.forEach(tree => {
                ctx.fillStyle = tree.color;
                ctx.fillRect(tree.x, tree.y, tree.width, tree.height);
            });
        }

        // Vẽ quái vật
        function drawMonsters() {
            monsters.forEach(monster => {
                ctx.fillStyle = monster.color;
                ctx.fillRect(monster.x, monster.y, monster.size, monster.size);
            });
        }

        // Xóa canvas
        function clearCanvas() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
        }

        // Cập nhật vị trí của cây
        function updateTrees() {
            trees.forEach(tree => {
                tree.x -= tree.speed;
                if (tree.x + tree.width < 0) {
                    tree.x = canvas.width; // Đặt lại vị trí cây khi ra khỏi khung hình
                }
            });
        }

        // Cập nhật vị trí của quái vật
        function updateMonsters() {
            monsters.forEach(monster => {
                monster.x -= monster.speed;
                if (monster.x + monster.size < 0) {
                    monster.x = canvas.width + Math.random() * 300; // Đặt lại vị trí quái vật
                }
            });
        }

        // Cập nhật vị trí Wukong
        function updateWukongPosition() {
            wukong.x += wukong.dx;
            wukong.y += wukong.dy;

            // Giữ Wukong trong giới hạn canvas
            if (wukong.x < 0) wukong.x = 0;
            if (wukong.y < 0) wukong.y = 0;
            if (wukong.x + wukong.size > canvas.width) wukong.x = canvas.width - wukong.size;
            if (wukong.y + wukong.size > canvas.height) wukong.y = canvas.height - wukong.size;
        }

        // Vòng lặp trò chơi
        function gameLoop() {
            clearCanvas();
            drawTrees(); // Vẽ cây
            drawMonsters(); // Vẽ quái vật
            drawWukong(); // Vẽ Wukong
            updateWukongPosition();
            updateTrees(); // Cập nhật vị trí cây
            updateMonsters(); // Cập nhật vị trí quái vật
            requestAnimationFrame(gameLoop);
        }

        // Điều khiển nhân vật
        function moveUp() {
            wukong.dy = -wukong.speed;
        }

        function moveDown() {
            wukong.dy = wukong.speed;
        }

        function moveLeft() {
            wukong.dx = -wukong.speed;
        }

        function moveRight() {
            wukong.dx = wukong.speed;
        }

        function stopMove() {
            wukong.dx = 0;
            wukong.dy = 0;
        }

        // Hành động vung gậy
        function attack() {
            wukong.isAttacking = true;
            setTimeout(() => {
                wukong.isAttacking = false;
            }, 200); // Gậy sẽ hiển thị trong 200ms
        }

        // Xử lý sự kiện bàn phím
        document.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowUp' || e.key === 'w') moveUp();
            if (e.key === 'ArrowDown' || e.key === 's') moveDown();
            if (e.key === 'ArrowLeft' || e.key === 'a') moveLeft();
            if (e.key === 'ArrowRight' || e.key === 'd') moveRight();
            if (e.key === ' ') attack(); // Phím cách (Space) để vung gậy
        });

        document.addEventListener('keyup', (e) => {
            if (['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight', 'w', 'a', 's', 'd'].includes(e.key)) stopMove();
        });

        // Bắt đầu vòng lặp trò chơi
        gameLoop();
    </script>
</body>
</html>
