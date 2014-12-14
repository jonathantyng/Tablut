var gameEl = document.querySelector('#tablutwindow');

var stage = new Kinetic.Stage({
	container : gameEl,
	width : 550,
	height : 550
});
var stageWidth = stage.attrs.width;
var stageHeight = stage.attrs.height;

// Constants
var EMPTY = 0;
var WHITE = 1;
var BLACK = 2;
var KING = 3;
var CORNER = 4;
var EDGE = 5;
var BOARD_SIZE = 9;
var NO_OF_WHITE = 8;
var NO_OF_BLACK = 16;

var SQUARE_SIZE = 50;
var MARGIN = 50;

// Layers
var boardLayer = new Kinetic.Layer();
var piecesLayer = new Kinetic.Layer();
var draggingLayer = new Kinetic.Layer();

// Image variables
var backgroundImg = new Image();
backgroundImg.src = 'Background.png';
var backgroundObj = new Kinetic.Image({
	x:0,
	y:0,
	height:550,
	width:550,
	image:backgroundImg
});
var boardImg = new Image();
boardImg.src = 'tablut.png';
var boardObj = new Kinetic.Image({
	x:MARGIN,
	y:MARGIN,
	height:450,
	width:450,
	image:boardImg
});

boardLayer.add(backgroundObj);
boardLayer.add(boardObj);

var blackImg = new Image();
blackImg.src = 'black.png';
var blackWinImg = new Image();
blackWinImg.src = 'blackWin.png';
var kingImg = new Image();
kingImg.src = 'king.png';
var mainMenuImg = new Image();
mainMenuImg.src = 'mainMenu.png';
var rulesImg = new Image();
rulesImg.src = 'rules.png';
var whiteImg = new Image();
whiteImg.src = 'white.png';
var whiteWinImg = new Image();
whiteWinImg.src = 'whiteWin.png';

var whiteList = new Array();
var blackList = new Array();

// Array to hold the board
var board = new Array(BOARD_SIZE + 2);
for (var i = 0; i <= BOARD_SIZE + 2; i++){
	board[i] = new Array(BOARD_SIZE + 2);
}
var corners = new Array(BOARD_SIZE + 2);
for (var i = 0; i <= BOARD_SIZE + 2; i++){
	corners[i] = new Array(BOARD_SIZE + 2);
}

// Initial board setup
var boardInput="5555555555554002220045500002000055000010000552000100025522113112255200010002550000100005500002000055400222004555555555555";
boardInput.split();
var stringCounter = 0;
for (var row = 0; row <= BOARD_SIZE + 1; row++){
		for(var col = 0; col <= BOARD_SIZE + 1; col++){
		board[row][col] = parseInt(boardInput[stringCounter]);
		corners[row][col] = -1;

		stringCounter++;
	}
}

// Sets up white and black pieces
for (var nextRow = 0; nextRow <= BOARD_SIZE + 1; nextRow++){
	for(var nextCol = 0; nextCol <= BOARD_SIZE + 1; nextCol++){
		if (board[nextRow][nextCol] == WHITE){
			whiteList.unshift(new Kinetic.Image({
				x:SQUARE_SIZE * (nextCol - 1) + MARGIN,
				y:SQUARE_SIZE * (nextRow - 1) + MARGIN,
				width:SQUARE_SIZE,
				height:SQUARE_SIZE,
				row:nextRow,
				col:nextCol,
				onBoard:true,
				colour:WHITE,
				image:whiteImg,
				draggable:true
			}));
			piecesLayer.add(whiteList[0]);
		}
		else if (board[nextRow][nextCol] == BLACK){
			blackList.push(new Kinetic.Image({
				x:SQUARE_SIZE * (nextCol - 1) + MARGIN,
				y:SQUARE_SIZE * (nextRow - 1) + MARGIN,
				width:SQUARE_SIZE,
				height:SQUARE_SIZE,
				row:nextRow,
				col:nextCol,
				onBoard:true,
				colour:BLACK,
				image:blackImg,
				draggable:false
			}));
			piecesLayer.add(blackList[blackList.length - 1]);
		}
	}
}
var king = new Kinetic.Image({
	x:SQUARE_SIZE * (5 - 1) + MARGIN,
	y:SQUARE_SIZE * (5 - 1) + MARGIN,
	width:SQUARE_SIZE,
	height:SQUARE_SIZE,
	row:5,
	col:5,
	onBoard:true,
	colour:KING,
	image:kingImg,
	draggable:true
});
piecesLayer.add(king);

// Sets corners
corners[1][1] = CORNER;
corners[1][BOARD_SIZE] = CORNER;
corners[BOARD_SIZE][1] = CORNER;
corners[BOARD_SIZE][BOARD_SIZE] = CORNER;
corners[5][5] = CORNER;
for (var border = 0; border < 11; border++){
	corners[0][border] = 6;
	corners[10][border]=6;
	corners[border][0] = 6;
	corners[border][10] = 6;
}

var capturedPieceList = new Array();
var selectedMoveInput;

// Variables to keep track of gameplay
var isGameOver = false;
var selectedPiece;
var playerTurn = 1; // 1 is white, 2 is black
var showRules = false;
var showMenu = true;
var gameMode; // 1 - Two Player



// ******************** Functions *************************

function rules(){
	showRules = true;
}

function mainMenu(){
	showMenu = true;
}

function gameOver(winner){
	isGameOver = winner;
	

	redraw();
}

function redraw(){
	boardLayer.draw();
	piecesLayer.draw();
	draggingLayer.draw();
}

function switchTurn(){
	if (playerTurn == 1){
		playerTurn = 2;
		for (var i = 0; i < whiteList.length; i++){
			whiteList[i].setAttr('draggable',false);
		}
		king.setAttr('draggable',false);
		for (var k = 0; k < blackList.length; k++){
			blackList[k].setAttr('draggable',true);
		}
	}else if (playerTurn == 2){
		playerTurn = 1;
		for (var i = 0; i < NO_OF_WHITE; i++){
			whiteList[i].setAttr('draggable',true);
		}
		king.setAttr('draggable',true);
		for (var k = 0; k< NO_OF_BLACK; k++){
			blackList[k].setAttr('draggable',false);
		}
	}
}

function validMove(piece,moveRow,moveCol){
	var row = piece.getAttr('row');
	var col = piece.getAttr('col');
	var type = piece.getAttr('colour');
	if ((moveRow == row && moveCol == col)
				|| (board[moveRow][moveCol] == CORNER && type != KING)
				|| (moveRow == BOARD_SIZE / 2 + 1
						&& moveCol == BOARD_SIZE / 2 + 1 && type != KING)
				|| board[moveRow][moveCol] == WHITE
				|| board[moveRow][moveCol] == BLACK) {
			return false;
		} else if (moveRow == row) {
			// Checks whether there is a piece in the way
			if (moveCol > col) {
				for (var nextCol = col + 1; nextCol < moveCol; nextCol++) {
					if (board[row][nextCol] != EMPTY) {
						return false;
					}
				}
			} else {
				for (var nextCol = col - 1; nextCol > moveCol; nextCol--) {
					if (board[row][nextCol] != EMPTY) {
						return false;
					}
				}
			}

			return true;
		} else if (moveCol == col) {
			if (moveRow > row) {
				for (var nextRow = row + 1; nextRow < moveRow; nextRow++) {
					if (board[nextRow][col] != EMPTY) {
						return false;
					}
				}
			} else {
				for (var nextRow = row - 1; nextRow > moveRow; nextRow--) {
					if (board[nextRow][col] != EMPTY) {
						return false;
					}
				}
			}

			return true;
		}

		return false;
}

function move(piece,row,col){
	//alert (piece.getAttr('row') + " " + piece.getAttr('col') + " to " + row + " " + col);

	board[piece.getAttr('row')][piece.getAttr('col')] = EMPTY;
	board[row][col] = piece.getAttr('colour');

	piece.setAttr('row', row);
	piece.setAttr('col', col);
	piece.setAttr('x',SQUARE_SIZE*col);
	piece.setAttr('y',SQUARE_SIZE*row);
}

function findPiece (row,col){
	if (playerTurn == BLACK) {
			for (var nextPiece = 0; nextPiece < whiteList.length; nextPiece++) {
				if (whiteList[nextPiece].getAttr('row') == row
						&& whiteList[nextPiece].getAttr('col') == col){
					return nextPiece;
				}
			}
		} else {
			for (var nextPiece = 0; nextPiece < blackList.length; nextPiece++) {
				if (blackList[nextPiece].getAttr('row') == row
						&& blackList[nextPiece].getAttr('col') == col){
					return nextPiece;
				}
			}
		}

		return -1;
}


function captureWhite(row,col){
	var index = -1;
	// Checks the four positions around the given position
	if (board[row][col + 1] == WHITE
			&& (board[row][col + 2] == BLACK || (corners[row][col + 2] == CORNER && board[row][col + 2] == EMPTY))) {
		board[row][col + 1] = EMPTY;
		index = findPiece(row, col + 1);
	} else if (board[row][col - 1] == WHITE
							&& (board[row][col - 2] == BLACK || (corners[row][col - 2] == CORNER && board[row][col - 2] == EMPTY))) {
		board[row][col - 1] = EMPTY;
		index = findPiece(row, col - 1);
	} else if (board[row + 1][col] == WHITE
							&& (board[row + 2][col] == BLACK || (corners[row + 2][col] == CORNER && board[row + 2][col] == EMPTY))) {
		board[row + 1][col] = EMPTY;
		index = findPiece(row + 1, col);
	} else if (board[row - 1][col] == WHITE
							&& (board[row - 2][col] == BLACK || (corners[row - 2][col] == CORNER && board[row - 2][col] == EMPTY))) {
		board[row - 1][col] = EMPTY;
		index = findPiece(row - 1, col);
	}
	// Checks if a king is captured
	if (board[row][col + 1] == KING
			&& (board[row][col + 2] == BLACK || corners[row][col + 2] >= 4)
			&& (board[row + 1][col + 1] == BLACK || corners[row + 1][col + 1] >= 4)
			&& (board[row - 1][col + 1] == BLACK || corners[row - 1][col + 1] >= 4)) {
				gameOver(2);
	} else if (board[row][col - 1] == KING
			&& (board[row][col - 2] == BLACK || corners[row][col - 2] >= 4)
			&& (board[row + 1][col - 1] == BLACK || corners[row + 1][col - 1] >= 4)
			&& (board[row - 1][col - 1] == BLACK || corners[row - 1][col - 1] >= 4)) {
				gameOver(2);
		return king;
	} else if (board[row + 1][col] == KING
			&& (board[row + 2][col] == BLACK || corners[row + 2][col] >= 4)
			&& (board[row + 1][col + 1] == BLACK || corners[row + 1][col + 1] >= 4)
			&& (board[row + 1][col - 1] == BLACK || corners[row + 1][col - 1] >= 4)) {
				gameOver(2);
		return king;
	} else if (board[row - 1][col] == KING
			&& (board[row - 2][col] == BLACK || corners[row - 2][col] >= 4)
			&& (board[row - 1][col + 1] == BLACK || corners[row - 1][col + 1] >= 4)
			&& (board[row - 1][col - 1] == BLACK || corners[row - 1][col - 1] >= 4)) {
				gameOver(2);
		return king;
	}

	if (index >= 0)
	{
		whiteList[index].setAttr('row', 20);
		whiteList[index].setAttr('col', 20);
		whiteList[index].setAttr('x',-300);
		whiteList[index].setAttr('y',-300);
	}
	else {
		return null;
	}
}


function captureBlack(row,col) {
		var index = -1;

		// Checks the four positions around the given position
		if (board[row][col + 1] == BLACK
				&& (board[row][col + 2] == WHITE
				|| corners[row][col + 2] == CORNER || board[row][col + 2] == KING)) {
			board[row][col + 1] = EMPTY;
			index = findPiece(row, col + 1);
		} else if (board[row][col - 1] == BLACK
				&& (board[row][col - 2] == WHITE
				|| corners[row][col - 2] == CORNER || board[row][col - 2] == KING)) {
			board[row][col - 1] = EMPTY;
			index = findPiece(row, col - 1);
		} else if (board[row + 1][col] == BLACK
				&& (board[row + 2][col] == WHITE
				|| corners[row + 2][col] == CORNER || board[row + 2][col] == KING)) {
			board[row + 1][col] = EMPTY;
			index = findPiece(row + 1, col);
		} else if (board[row - 1][col] == BLACK
				&& (board[row - 2][col] == WHITE
				|| corners[row - 2][col] == CORNER || board[row - 2][col] == KING)) {
			board[row - 1][col] = EMPTY;
			index = findPiece(row - 1, col);
		}

		if (index >= 0){
			blackList[index].setAttr('row', 20);
			blackList[index].setAttr('col', 20);
			blackList[index].setAttr('x',-300);
			blackList[index].setAttr('y',-300);
		}
		return null;
}


// ******************** Listeners *************************


piecesLayer.on('dragend', function(evt){
	var piece = evt.target;
	var mousePos = stage.getPointerPosition();
	var origRow = piece.getAttr('row');
	var origCol = piece.getAttr('col');
	var row = Math.floor(((mousePos.y - MARGIN)/SQUARE_SIZE)) + 1;
	var col = Math.floor(((mousePos.x - MARGIN)/SQUARE_SIZE)) + 1;
	//alert(row + " " + col);

	if (validMove(piece,row,col)){
		move(piece,row,col);

		if (playerTurn == WHITE){
			if (piece.getAttr('colour') == KING
						&& board[row][col] == CORNER) {
					gameOver(1);
				}
			captureBlack(row,col);
		} else if (playerTurn == BLACK) {
			captureWhite(row,col);
		}
		switchTurn();
	} else {
		// Resets the dragged piece to its original position
		move(piece,origRow,origCol);
	}

	piecesLayer.draw();
});



// Draws layers
stage.add(boardLayer);
stage.add(piecesLayer);
stage.add(draggingLayer);
stage.draw();
boardLayer.draw();
piecesLayer.draw();
draggingLayer.draw();

setInterval(redraw,1000);
