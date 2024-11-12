# Bomberman
2player game
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bomberman 2 Player</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>

<div id="gameArea"></div>
<script src="script.js"></script>
</body>
</html>

body {
    font-family: Arial, sans-serif;
    background-color: #f0f0f0;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    margin: 0;
}

#gameArea {
    width: 400px;
    height: 400px;
    display: grid;
    grid-template-columns: repeat(10, 40px);
    grid-template-rows: repeat(10, 40px);
    gap: 5px;
    background-color: #222;
}

.cell {
    width: 40px;
    height: 40px;
    background-color: #fff;
    border: 1px solid #ccc;
}

.wall {
    background-color: #333;
}

.player1 {
    background-color: red;
}

.player2 {
    background-color: blue;
}

.bomb {
    background-color: yellow;
}

const gameArea = document.getElementById('gameArea');
const gridSize = 10; // 10x10 grid
let player1Pos = { x: 1, y: 1 }; // Player 1 starting position (red)
let player2Pos = { x: 8, y: 8 }; // Player 2 starting position (blue)
let bombs = [];

function createGameBoard() {
    gameArea.innerHTML = '';
    for (let row = 0; row < gridSize; row++) {
        for (let col = 0; col < gridSize; col++) {
            const cell = document.createElement('div');
            cell.classList.add('cell');
            if (Math.random() < 0.2) cell.classList.add('wall'); // Random walls
            gameArea.appendChild(cell);
        }
    }
    placePlayer(player1Pos, 'player1');
    placePlayer(player2Pos, 'player2');
}

function placePlayer(position, playerClass) {
    const cells = document.querySelectorAll('.cell');
    const playerCell = cells[position.y * gridSize + position.x];
    playerCell.classList.add(playerClass);
}

function placeBomb(player) {
    const bombPos = player === 1 ? { ...player1Pos } : { ...player2Pos };
    bombs.push(bombPos);

    const cells = document.querySelectorAll('.cell');
    const bombCell = cells[bombPos.y * gridSize + bombPos.x];
    bombCell.classList.add('bomb');

    setTimeout(() => {
        explodeBomb(bombPos);
    }, 2000); // Bomb explodes after 2 seconds
}

function explodeBomb(bombPos) {
    const cells = document.querySelectorAll('.cell');
    // Explosion effect: remove bombs in surrounding cells
    for (let dx = -1; dx <= 1; dx++) {
        for (let dy = -1; dy <= 1; dy++) {
            const newX = bombPos.x + dx;
            const newY = bombPos.y + dy;
            if (newX >= 0 && newY >= 0 && newX < gridSize && newY < gridSize) {
                const cellIndex = newY * gridSize + newX;
                cells[cellIndex].classList.remove('wall', 'bomb', 'player1', 'player2');
            }
        }
    }
}

function movePlayer(direction, player) {
    let position = player === 1 ? player1Pos : player2Pos;
    switch (direction) {
        case 'up':
            if (position.y > 0) position.y--;
            break;
        case 'down':
            if (position.y < gridSize - 1) position.y++;
            break;
        case 'left':
            if (position.x > 0) position.x--;
            break;
        case 'right':
            if (position.x < gridSize - 1) position.x++;
            break;
    }
    if (player === 1) {
        player1Pos = position;
    } else {
        player2Pos = position;
    }
    updateGameBoard();
}

function updateGameBoard() {
    const cells = document.querySelectorAll('.cell');
    cells.forEach(cell => cell.classList.remove('player1', 'player2'));
    placePlayer(player1Pos, 'player1');
    placePlayer(player2Pos, 'player2');
}

document.addEventListener('keydown', function (event) {
    switch (event.key) {
        case 'ArrowUp':
            movePlayer('up', 1);
            break;
        case 'ArrowDown':
            movePlayer('down', 1);
            break;
        case 'ArrowLeft':
            movePlayer('left', 1);
            break;
        case 'ArrowRight':
            movePlayer('right', 1);
            break;
        case 'w':
            movePlayer('up', 2);
            break;
        case 's':
            movePlayer('down', 2);
            break;
        case 'a':
            movePlayer('left', 2);
            break;
        case 'd':
            movePlayer('right', 2);
            break;
        case ' ':
            placeBomb(1);
            break;
        case 'Enter':
            placeBomb(2);
            break;
    }
});

createGameBoard();
