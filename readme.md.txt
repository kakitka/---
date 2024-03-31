#Проект Крестики и Нолики

Веб версия игры крестики и нолики на одного человека. 
Программа предоставляет бота с МиниМакс стратегией в 
противники игроку.

____
##ребования

Любой браузер.

____
##Разработка

Проект разделен на три части:
x0.html
```html
<!DOCTYPE html>
<html lang="ru" >
<head>
  <meta charset="UTF-8">
  <title>Крестики-нолики</title>
  <link rel="stylesheet" href="x0.css">

</head>
<body>
<div>
  <h1 class="nav">Крестики и Нолики</h1>
</div>
<div>
  <button class="btn" onClick="startGame()">Заново</button>
</div>
<div>
	<table>
		<tr>
			<td class="cell" id="0"></td>
			<td class="cell" id="1"></td>
			<td class="cell" id="2"></td>
		</tr>
		<tr>
			<td class="cell" id="3"></td>
			<td class="cell" id="4"></td>
			<td class="cell" id="5"></td>
		</tr>
		<tr>
			<td class="cell" id="6"></td>
			<td class="cell" id="7"></td>
			<td class="cell" id="8"></td>
		</tr>
	</table>
	</div>
	<div class="endgame">
		<div class="text"></div>
	</div>
  <script src="x0.js"></script>
</body>
</html>
```

x0.css
```css
@import url('https://fonts.googleapis.com/css2?family=Roboto:wght@100&family=Rubik+Doodle+Shadow&family=Titillium+Web:ital,wght@0,200;0,300;0,400;0,600;1,200;1,300;1,400&display=swap');

td {
	border:  2px solid #333;
	height:  100px;
	width:  100px;
	text-align:  center;
	vertical-align:  middle;
	font-family:  'Oswald', sans-serif;
	font-size:  70px;
	cursor: pointer;
}


table {
	border-collapse: collapse;
	position: absolute;
	left: 52%;
	margin-left: -155px;
	top: 50px;
	display:inline;
	top: 200px;
}

table tr:first-child td {
	border-top: 0;
}

table tr:last-child td {
	border-bottom: 0;
}

table tr td:first-child {
	border-left: 0;
}

table tr td:last-child {
	border-right: 0;
}


.endgame {
  display: none;
  width: 200px;
  top: 270px;
  position: absolute;
  left: 52%;
  background-color: rgba(0,128,0, 0.8);
  margin-left: -100px;
  padding-top: 50px;
  padding-bottom: 50px;
  text-align: center;
  border-radius: 5px;
  color: white;
  font-size: 2em;
  font-family:  'Oswald', sans-serif
}

.btn {
	border: none;
	padding: 16px 0;
	border-radius: 4px;
	color: #fff;
	background-color: #00ff7f;
	cursor: pointer;
	transition: background-color 0.3s ease-in-out;
	display: inline-block;
	width: 246px;
	left: 45%;
	align-items: center;
	display:inline;
	position:relative;
	top: 20px
	
}

.nav {
	align-items: center;
	left: 45%;
	font-family:  'Oswald', sans-serif;
	white-space:pre-wrap;
	display:inline;
	position:relative;
}
```

x0.js
```js
var origBoard;

const huPlayer = 'X';
const aiPlayer = 'O';

const winCombos = [
	[0, 1, 2],
	[3, 4, 5],
	[6, 7, 8],
	[0, 3, 6],
	[1, 4, 7],
	[2, 5, 8],
	[0, 4, 8],
	[6, 4, 2]
]

const cells = document.querySelectorAll('.cell');
startGame();

function startGame() {
	document.querySelector(".endgame").style.display = "none";
	origBoard = Array.from(Array(9).keys());
	for (var i = 0; i < cells.length; i++) {
		cells[i].innerText = '';
		cells[i].style.removeProperty('background-color');
		cells[i].addEventListener('click', turnClick, false);
	}
}

function turnClick(square) {
	if (typeof origBoard[square.target.id] == 'number') {
		turn(square.target.id, huPlayer)
		if (!checkWin(origBoard, huPlayer) && !checkTie()) turn(bestSpot(), aiPlayer);
	}
}

function turn(squareId, player) {
	origBoard[squareId] = player;
	document.getElementById(squareId).innerText = player;
	let gameWon = checkWin(origBoard, player)
	if (gameWon) gameOver(gameWon)
}


function checkWin(board, player) {
	let plays = board.reduce((a, e, i) =>
		(e === player) ? a.concat(i) : a, []);
	let gameWon = null;
	for (let [index, win] of winCombos.entries()) {
		if (win.every(elem => plays.indexOf(elem) > -1)) {
			gameWon = {index: index, player: player};
			break;
		}
	}
	return gameWon;
}

function gameOver(gameWon) {
	for (let index of winCombos[gameWon.index]) {
		document.getElementById(index).style.backgroundColor =
			gameWon.player == huPlayer ? "blue" : "red";
	}
	for (var i = 0; i < cells.length; i++) {
		cells[i].removeEventListener('click', turnClick, false);
	}
	declareWinner(gameWon.player == huPlayer ? "Вы выиграли!" : "Вы проиграли.");
}

function declareWinner(who) {
	document.querySelector(".endgame").style.display = "block";
	document.querySelector(".endgame .text").innerText = who;
}

function emptySquares() {
	return origBoard.filter(s => typeof s == 'number');
}

function bestSpot() {
	return minimax(origBoard, aiPlayer).index;
}

function checkTie() {
	if (emptySquares().length == 0) {
		for (var i = 0; i < cells.length; i++) {
			cells[i].style.backgroundColor = "green";
			cells[i].removeEventListener('click', turnClick, false);
		}
		declareWinner("Ничья!")
		return true;
	}
	return false;
}

function minimax(newBoard, player) {
	var availSpots = emptySquares();

	if (checkWin(newBoard, huPlayer)) {
		return {score: -10};
	} else if (checkWin(newBoard, aiPlayer)) {
		return {score: 10};
	} else if (availSpots.length === 0) {
		return {score: 0};
	}

	var moves = [];
	for (var i = 0; i < availSpots.length; i++) {
		var move = {};
		move.index = newBoard[availSpots[i]];
		newBoard[availSpots[i]] = player;

		if (player == aiPlayer) {
			var result = minimax(newBoard, huPlayer);
			move.score = result.score;
		} else {
			var result = minimax(newBoard, aiPlayer);
			move.score = result.score;
		}
		newBoard[availSpots[i]] = move.index;
		moves.push(move);
	}
	var bestMove;
	if(player === aiPlayer) {
		var bestScore = -10000;
		for(var i = 0; i < moves.length; i++) {
			if (moves[i].score > bestScore) {
				bestScore = moves[i].score;
				bestMove = i;
			}
		}
	} else {
		var bestScore = 10000;
		for(var i = 0; i < moves.length; i++) {
			if (moves[i].score < bestScore) {
				bestScore = moves[i].score;
				bestMove = i;
			}
		}
	}
	return moves[bestMove];
}
```
____
##FAQ

Проект был создан для получении баллов по предмету "Система поддержки принятия решений"
