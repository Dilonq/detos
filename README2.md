<head>
<style>
.button {
  border: none;
  color: white;
  padding: 16px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  transition-duration: 0.4s;
  cursor: pointer;
}

.button1 {
  background-color: white; 
  color: black; 
  border: 2px solid #4CAF50;
}

.button1:hover {
  background-color: #4CAF50;
  color: white;
}

.button2 {
  background-color: white; 
  color: black; 
  border: 2px solid #008CBA;
}

.button2:hover {
  background-color: #008CBA;
  color: white;
}

</style>
</head>

<!-- Site not done message -->
<p style="text-align: center">
    <span style="font-size:1em">
        Sites not done yet
    </span>
</p>

<!-- About Me Stuff -->
<p style="text-align: center">
    <span style="font-size:1em">
        <a href="info/about.html">
            About Me
        </a>
    </span>
</p>

<!-- Breakout game -->
<p style="text-align: center;"><button onclick="Start()">Start</button></p>
<head>
    <meta charset="utf-8" />
    <title>Breakout</title>
    <style>  
        * { padding: 0; margin: 0; }
        canvas { background: #eee; display: block; margin: 0 auto; }
    </style>
</head>
<body>

<canvas id="myCanvas" width="480" height="320"></canvas>

<script>
// set up canvas
var canvas = document.getElementById("myCanvas");
var ctx = canvas.getContext("2d");
// game variables
var x = canvas.width/2;
var y = canvas.height-30;

var paddleHeight = 10;
var paddleWidth = 75;

var paddleX = (canvas.width-paddleWidth) / 2;

var ballRadius = 10;

var speed = 2;
var dx = speed;
var dy = 0-speed;

var rightPressed = false;
var leftPressed = false;

var brickRowCount = 3;
var brickColumnCount = 5;
var brickWidth = 75;
var brickHeight = 20;
var brickPadding = 10;
var brickOffsetTop = 30;
var brickOffsetLeft = 30;

var bricks = [];
for(var c=0; c<brickColumnCount; c++) {
    bricks[c] = [];
    for(var r=0; r<brickRowCount; r++) {
        bricks[c][r] = { x: 0, y: 0, status: 1 };
    }
}

var score = 0;
var start = false;

function ResetVars(){
    dx = speed
    dy = 0-speed

    x = canvas.width/2;
    y = canvas.height-30;

    bricks = [];
    for(var c=0; c<brickColumnCount; c++) {
        bricks[c] = [];
        for(var r=0; r<brickRowCount; r++) {
            bricks[c][r] = { x: 0, y: 0, status: 1 };
        }
    }

    score = 0;
}

document.addEventListener("keydown", keyDownHandler, false);
document.addEventListener("keyup", keyUpHandler, false);
document.addEventListener("mousemove", mouseMoveHandler, false);

// set up screen to look nice
ctx.clearRect(0, 0, canvas.width, canvas.height);
drawPaddle();
drawBall();
drawBricks();
collisionDetection();
drawScore();

function keyDownHandler(e) {
    if(e.key == "Right" || e.key == "ArrowRight") {
        rightPressed = true;
    }
    else if(e.key == "Left" || e.key == "ArrowLeft") {
        leftPressed = true;
    }
}

function keyUpHandler(e) {
    if(e.key == "Right" || e.key == "ArrowRight") {
        rightPressed = false;
    }
    else if(e.key == "Left" || e.key == "ArrowLeft") {
        leftPressed = false;
    }
}

function drawBall() {
    ctx.beginPath();
    ctx.arc(x, y, ballRadius, 0, Math.PI*2);
    ctx.fillStyle = "#0095DD";
    ctx.fill();
    ctx.closePath();
}

function drawPaddle() {
    ctx.beginPath();
    ctx.rect(paddleX, canvas.height-paddleHeight, paddleWidth, paddleHeight);
    ctx.fillStyle = "#0095DD";
    ctx.fill();
    ctx.closePath();
}

function drawBricks() {
    for(var c=0; c<brickColumnCount; c++) {
        for(var r=0; r<brickRowCount; r++) {
            if(bricks[c][r].status == 1) {
                var brickX = (c*(brickWidth+brickPadding))+brickOffsetLeft;
                var brickY = (r*(brickHeight+brickPadding))+brickOffsetTop;
                bricks[c][r].x = brickX;
                bricks[c][r].y = brickY;
                ctx.beginPath();
                ctx.rect(brickX, brickY, brickWidth, brickHeight);
                ctx.fillStyle = "#0095DD";
                ctx.fill();
                ctx.closePath();
            }
        }
    }
}

function collisionDetection() {
    for(var c=0; c<brickColumnCount; c++) {
        for(var r=0; r<brickRowCount; r++) {
            var b = bricks[c][r];
            if(b.status == 1) {
                if(x > b.x && x < b.x+brickWidth && y > b.y && y < b.y+brickHeight) {
                    dy = -dy;
                    b.status = 0;
                    score++;
                    if(score == brickRowCount*brickColumnCount) {
                        alert("thanks for playing!");
                        start = false;
                        ResetVars();
                        document.location.reload();
                        clearInterval(interval); // Needed for Chrome to end game
                    }
                }
            }
        }
    }
}

function drawScore() {
    ctx.font = "16px Arial";
    ctx.fillStyle = "#000000";
    ctx.fillText("Score: "+score, 8, 20);
}

function mouseMoveHandler(e) {
    var relativeX = e.clientX - canvas.offsetLeft;
    if(relativeX > 0 && relativeX < canvas.width) {
        paddleX = relativeX - paddleWidth/2;
    }
}

function Start(){
    ResetVars();
    start = true;
}

function draw() {
    if (start == true){
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        drawPaddle();
        drawBall();
        drawBricks();
        collisionDetection();
        drawScore();

        if(x + dx > canvas.width-ballRadius || x + dx < ballRadius) {
            dx = -dx;
        }
        if(y + dy < ballRadius) {
            dy = -dy;
        } else if(y + dy > canvas.height-ballRadius) {
            if(x > paddleX && x < paddleX + paddleWidth) {
                dy = -dy;
            }
            else {
                document.location.reload();
                clearInterval(interval);
                start = false;
                ResetVars();
            }
        }

        if(rightPressed) {
            paddleX += 7;
            if (paddleX + paddleWidth > canvas.width){
                paddleX = canvas.width - paddleWidth;
            }
        }
        else if(leftPressed) {
            paddleX -= 7;
            if (paddleX < 0){
                paddleX = 0;
            }
        }
        
        x += dx;
        y += dy;
    } else {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        drawPaddle();
        drawBall();
        drawBricks();
        collisionDetection();
        drawScore();
    }
}
var interval = setInterval(draw, 10);

</script>

</body>
